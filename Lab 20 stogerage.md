# Hướng Dẫn Thực Hành Chi Tiết: Quản Lý Lưu Trữ Trên Linux

## Phần 1: Chuẩn Bị Môi Trường

### 1.1. Kiểm Tra Hệ Thống
```bash
# Xem thông tin ổ đĩa hiện có
lsblk
fdisk -l

# Giải thích:
# - lsblk: Liệt kê các thiết bị lưu trữ dạng block
# - fdisk -l: Xem chi tiết bảng phân vùng
# - Cần nắm rõ cấu trúc ổ đĩa trước khi thao tác để tránh mất dữ liệu
```

### 1.2. Tạo Môi Trường Lab
```bash
# Trong VirtualBox, thêm 3 ổ đĩa ảo:
1. Tắt máy ảo
2. Settings -> Storage -> Add new disk
3. Thêm sdb, sdc, sdd (mỗi ổ 10GB)

# Tại sao cần 3 ổ?
- sdb: Thực hành MBR
- sdc: Thực hành GPT
- sdd: Thực hành LVM
```

## Phần 2: Tìm Hiểu Về Phân Vùng

### 2.1. Phân Biệt MBR và GPT

#### MBR (Master Boot Record):
- Giới hạn 4 phân vùng chính
- Dung lượng tối đa 2TB
- Phù hợp với hệ thống cũ

#### GPT (GUID Partition Table):
- Hỗ trợ đến 128 phân vùng
- Không giới hạn dung lượng thực tế
- Tương thích với UEFI

### 2.2. Thực Hành Với Parted - MBR
```bash
# 1. Tạo bảng phân vùng MBR
sudo parted /dev/sdb mklabel msdos

# 2. Tạo phân vùng
sudo parted /dev/sdb mkpart primary ext4 1MiB 5GiB

# Tại sao dùng đơn vị MiB?
- Đảm bảo căn chỉnh cho hiệu năng tốt
- Tránh lỗi tính toán
- Chuẩn trong công nghiệp
```

### 2.3. Thực Hành Với Parted - GPT
```bash
# 1. Tạo bảng phân vùng GPT
sudo parted /dev/sdc mklabel gpt

# 2. Tạo phân vùng
sudo parted /dev/sdc mkpart data ext4 1MiB 4GiB

# Tại sao chọn GPT?
- Chuẩn mới và hiện đại
- Không giới hạn số phân vùng chính
- Tự động sao lưu metadata
```

## Phần 3: Quản Lý Phân Vùng Linh Hoạt (LVM)

### 3.1. Tìm Hiểu LVM
```bash
# Các thành phần:
1. Physical Volumes (PV): 
   - Ổ đĩa vật lý
   - Được chia thành các PE (Physical Extents)

2. Volume Groups (VG):
   - Nhóm các PV
   - Như một "ổ đĩa ảo lớn"

3. Logical Volumes (LV):
   - "Phân vùng ảo"
   - Có thể thay đổi kích thước dễ dàng

# Tại sao dùng LVM?
- Quản lý không gian linh hoạt
- Dễ dàng mở rộng/thu nhỏ
- Hỗ trợ snapshot
```

### 3.2. Thực Hành LVM
```bash
# 1. Tạo Physical Volume
sudo pvcreate /dev/sdd
sudo pvdisplay

# 2. Tạo Volume Group
sudo vgcreate data_vg /dev/sdd
sudo vgdisplay

# 3. Tạo Logical Volume
sudo lvcreate -n data_lv -L 5G data_vg

# Tại sao phải qua 3 bước?
- Phân cấp quản lý rõ ràng
- Dễ mở rộng từng cấp độ
- Linh hoạt trong quản lý
```

### 3.3. Tạo Và Quản Lý Filesystem
```bash
# 1. Tạo filesystem ext4
sudo mkfs.ext4 /dev/data_vg/data_lv

# 2. Mount và sử dụng
sudo mkdir /mnt/data
sudo mount /dev/data_vg/data_lv /mnt/data

# 3. Cấu hình auto mount
sudo nano /etc/fstab
/dev/data_vg/data_lv /mnt/data ext4 defaults 0 2

# Tại sao chọn ext4?
- Ổn định và đáng tin cậy
- Dễ sửa chữa khi có lỗi
- Hiệu năng tốt cho mọi mục đích
```

## Phần 4: Thực Hành Nâng Cao

### 4.1. Snapshot và Backup
```bash
# 1. Tạo snapshot
sudo lvcreate -s -n data_snap -L 1G /dev/data_vg/data_lv

# Tại sao cần snapshot?
- Backup nhanh tại thời điểm
- Test thay đổi an toàn
- Khôi phục nhanh chóng
```

### 4.2. Mở Rộng Không Gian
```bash
# 1. Thêm ổ đĩa mới
sudo pvcreate /dev/sde
sudo vgextend data_vg /dev/sde

# 2. Mở rộng logical volume
sudo lvextend -L +5G /dev/data_vg/data_lv
sudo resize2fs /dev/data_vg/data_lv

# Tại sao mở rộng online được?
- Filesystem hiện đại hỗ trợ
- Không cần dừng hệ thống
- Đảm bảo hoạt động liên tục
```

## Phần 5: Bài Tập Thực Hành

### Bài Tập 1: Thiết Lập Storage Cho Web Server
```bash
# Yêu cầu:
1. Tạo LVM với 10GB cho web data
2. Mount vào /var/www
3. Cấu hình backup tự động

# Tại sao làm vậy?
- Quản lý dung lượng web riêng biệt
- Dễ backup và restore
- Mở rộng dễ dàng khi cần
```

## Các Lưu Ý Quan Trọng

1. **Lập Kế Hoạch:**
   - Dự tính nhu cầu tương lai
   - Để dự phòng cho snapshot
   - Ghi chép cấu hình đầy đủ

2. **Giám Sát:**
   - Kiểm tra định kỳ
   - Cảnh báo khi gần hết dung lượng
   - Phân tích xu hướng sử dụng

3. **Bảo Mật:**
   - Backup thường xuyên
   - Test khôi phục
   - Ghi chép quy trình

4. **Hiệu Năng:**
   - Căn chỉnh phân vùng đúng
   - Chọn filesystem phù hợp
   - Theo dõi I/O

