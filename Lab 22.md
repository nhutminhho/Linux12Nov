# Bài Tập Thực Hành Chi Tiết: Quản Lý Storage Trên Linux

## Bài Tập: Thiết Lập Storage Cho Web Server

### Yêu Cầu Bài Tập
1. Thêm ổ cứng mới 20GB
2. Tạo LVM với 2 phân vùng:
   - 10GB cho web data (/var/www)
   - 5GB cho backup (/backup)
3. Cấu hình auto mount
4. Thiết lập backup tự động

### Phần 1: Chuẩn Bị Môi Trường

#### Bước 1: Thêm Ổ Cứng Mới
```bash
# 1. Tắt máy ảo
sudo shutdown -h now

# 2. Trong VirtualBox:
- Settings -> Storage
- Add new disk (20GB)
- Start máy ảo

# 3. Kiểm tra ổ mới
lsblk
# Kết quả mong đợi: thấy sdb 20GB

# Giải thích:
- Thêm ổ mới để không ảnh hưởng dữ liệu hiện có
- 20GB đủ cho web data và backup
- Kiểm tra bằng lsblk để xác nhận ổ được nhận đúng
```

### Phần 2: Tạo Phân Vùng và LVM

#### Bước 1: Tạo Physical Volume
```bash
# 1. Kiểm tra ổ đĩa
sudo fdisk -l /dev/sdb

# 2. Tạo PV
sudo pvcreate /dev/sdb
sudo pvs

# Giải thích:
- fdisk -l xem thông tin chi tiết ổ đĩa
- pvcreate chuẩn bị ổ đĩa cho LVM
- pvs kiểm tra PV đã tạo
```

#### Bước 2: Tạo Volume Group
```bash
# 1. Tạo VG tên webdata_vg
sudo vgcreate webdata_vg /dev/sdb
sudo vgs

# 2. Kiểm tra chi tiết
sudo vgdisplay webdata_vg

# Giải thích:
- webdata_vg: tên dễ nhớ và mô tả mục đích
- vgs: xem thông tin tổng quan
- vgdisplay: xem chi tiết dung lượng và PE
```

#### Bước 3: Tạo Logical Volumes
```bash
# 1. Tạo LV cho web data
sudo lvcreate -n www_lv -L 10G webdata_vg

# 2. Tạo LV cho backup
sudo lvcreate -n backup_lv -L 5G webdata_vg

# 3. Kiểm tra
sudo lvs
sudo lvdisplay

# Giải thích:
- -n: đặt tên LV
- -L: chỉ định dung lượng
- Để lại 5GB dự phòng cho snapshot
```

### Phần 3: Tạo và Mount Filesystem

#### Bước 1: Tạo Filesystem
```bash
# 1. Format LV với ext4
sudo mkfs.ext4 /dev/webdata_vg/www_lv
sudo mkfs.ext4 /dev/webdata_vg/backup_lv

# Giải thích:
- ext4: filesystem ổn định và phổ biến
- Hỗ trợ tính năng journal và recovery
- Hiệu năng tốt cho web server
```

#### Bước 2: Tạo Mount Points
```bash
# 1. Tạo thư mục mount
sudo mkdir -p /var/www
sudo mkdir -p /backup

# 2. Mount thử
sudo mount /dev/webdata_vg/www_lv /var/www
sudo mount /dev/webdata_vg/backup_lv /backup

# 3. Kiểm tra
df -h

# Giải thích:
- mkdir -p: tạo cả thư mục cha nếu chưa có
- mount thử để kiểm tra trước khi cấu hình auto
```

#### Bước 3: Cấu Hình Auto Mount
```bash
# 1. Backup fstab
sudo cp /etc/fstab /etc/fstab.backup

# 2. Thêm vào /etc/fstab
sudo bash -c 'echo "/dev/webdata_vg/www_lv /var/www ext4 defaults 0 2" >> /etc/fstab'
sudo bash -c 'echo "/dev/webdata_vg/backup_lv /backup ext4 defaults 0 2" >> /etc/fstab'

# 3. Test cấu hình
sudo mount -a

# Giải thích:
- Backup phòng khi có lỗi
- defaults: các option mount cơ bản
- mount -a: test không bị lỗi
```

### Phần 4: Thiết Lập Backup

#### Bước 1: Tạo Script Backup
```bash
# 1. Tạo script
sudo nano /backup/backup_www.sh

# 2. Thêm nội dung
#!/bin/bash
DATE=$(date +%Y%m%d)
BACKUP_DIR="/backup/www_$DATE"
mkdir -p $BACKUP_DIR
rsync -av --delete /var/www/ $BACKUP_DIR/
find /backup -type d -mtime +7 -exec rm -rf {} \;

# 3. Phân quyền
sudo chmod +x /backup/backup_www.sh

# Giải thích:
- Tạo thư mục backup theo ngày
- rsync: copy hiệu quả, chỉ copy thay đổi
- Tự động xóa backup cũ hơn 7 ngày
```

#### Bước 2: Tạo Cron Job
```bash
# 1. Mở crontab
sudo crontab -e

# 2. Thêm job
0 1 * * * /backup/backup_www.sh

# Giải thích:
- Chạy lúc 1 giờ sáng hàng ngày
- Thời điểm ít tải để không ảnh hưởng performance
```

### Phần 5: Kiểm Tra và Test

#### Bước 1: Test Web Storage
```bash
# 1. Tạo file test
sudo bash -c 'echo "Test web content" > /var/www/index.html'

# 2. Kiểm tra quyền
ls -l /var/www/index.html

# 3. Kiểm tra dung lượng
df -h /var/www
```

#### Bước 2: Test Backup
```bash
# 1. Chạy backup thử
sudo /backup/backup_www.sh

# 2. Kiểm tra kết quả
ls -l /backup/
cat /backup/www_$(date +%Y%m%d)/index.html
```

### Phần 6: Xử Lý Sự Cố

#### Các Lỗi Thường Gặp
1. **Mount thất bại:**
```bash
# Kiểm tra log
dmesg | tail
# Kiểm tra fstab syntax
sudo mount -av
```

2. **Backup không chạy:**
```bash
# Kiểm tra cron log
sudo tail /var/log/cron
# Test script với -x
bash -x /backup/backup_www.sh
```

### Best Practices

1. **Security:**
   - Giới hạn quyền truy cập /backup
   - Log đầy đủ backup activity
   - Kiểm tra tính toàn vẹn backup

2. **Monitoring:**
   - Theo dõi dung lượng sử dụng
   - Kiểm tra backup logs
   - Alert khi gần hết space

3. **Documentation:**
   - Ghi chép cấu hình
   - Quy trình restore
   - Lịch backup và retention

