# Lab Thực Hành: Quản Lý Storage Trên Linux

## Phần 1: Tìm Hiểu về Storage Management

### 1.1. Tại Sao Cần Quản Lý Storage?
- **Mục đích:**
  - Tối ưu hóa không gian lưu trữ
  - Mở rộng linh hoạt
  - Bảo vệ dữ liệu
  - Quản lý hiệu quả tài nguyên

### 1.2. Chuẩn Bị Môi Trường
```bash
# 1. Kiểm tra disks hiện có
lsblk

# 2. Xem partition tables
sudo fdisk -l

# Giải thích:
# - lsblk: Hiển thị cấu trúc block devices
# - fdisk -l: Xem chi tiết partition tables
# - Cần nắm rõ layout trước khi thay đổi
```

## Phần 2: Thực Hành với Parted

### Lab 2.1: Tạo và Quản Lý MBR Partitions
```bash
# 1. Mở parted với disk mới
sudo parted /dev/sdb

# 2. Tạo MBR partition table
mklabel msdos

# 3. Tạo partition
mkpart primary ext4 1MiB 1GiB

# 4. Kiểm tra kết quả
print
quit

# Giải thích:
# - msdos = MBR partition table
# - 1MiB: Để căn chỉnh tốt cho hiệu năng
# - primary: Loại partition phổ biến nhất
```

### Lab 2.2: Tạo và Quản Lý GPT Partitions
```bash
# 1. Tạo GPT partition table
sudo parted /dev/sdc
mklabel gpt

# 2. Tạo partitions
mkpart primary ext4 1MiB 2GiB
mkpart primary ext4 2GiB 4GiB

# Giải thích:
# - GPT hỗ trợ disks >2TB
# - Nhiều partitions hơn MBR
# - Metadata được backup tự động
```

## Phần 3: Logical Volume Management (LVM)

### Lab 3.1: Tạo Physical Volumes
```bash
# 1. Kiểm tra disks
sudo fdisk -l

# 2. Tạo physical volume
sudo pvcreate /dev/sdb
sudo pvcreate /dev/sdc

# 3. Kiểm tra PVs
sudo pvs
sudo pvdisplay

# Giải thích:
# - PV là basic block của LVM
# - Có thể dùng whole disk hoặc partition
# - pvcreate khởi tạo metadata LVM
```

### Lab 3.2: Tạo Volume Groups
```bash
# 1. Tạo volume group
sudo vgcreate data_vg /dev/sdb /dev/sdc

# 2. Kiểm tra VG
sudo vgs
sudo vgdisplay

# Giải thích:
# - VG nhóm các PV thành pool
# - Dễ dàng mở rộng/thu nhỏ
# - Quản lý tập trung space
```

### Lab 3.3: Tạo Logical Volumes
```bash
# 1. Tạo logical volume
sudo lvcreate -n data_lv -L 2G data_vg

# 2. Tạo filesystem
sudo mkfs.ext4 /dev/data_vg/data_lv

# 3. Mount và sử dụng
sudo mkdir /mnt/data
sudo mount /dev/data_vg/data_lv /mnt/data

# Giải thích:
# - LV là "phân vùng ảo"
# - Linh hoạt trong resize
# - Có thể snapshot
```

## Phần 4: Mở Rộng Storage

### Lab 4.1: Mở Rộng LVM
```bash
# 1. Thêm PV mới
sudo pvcreate /dev/sdd

# 2. Mở rộng VG
sudo vgextend data_vg /dev/sdd

# 3. Mở rộng LV
sudo lvextend -L +1G /dev/data_vg/data_lv

# 4. Resize filesystem
sudo resize2fs /dev/data_vg/data_lv

# Giải thích:
# - Mở rộng online không cần unmount
# - Thực hiện theo trình tự PV->VG->LV
# - resize2fs cập nhật filesystem
```

## Phần 5: Snapshot và Backup

### Lab 5.1: Tạo LVM Snapshots
```bash
# 1. Tạo snapshot
sudo lvcreate -s -n data_snap -L 500M /dev/data_vg/data_lv

# 2. Mount snapshot để check
sudo mkdir /mnt/snap
sudo mount /dev/data_vg/data_snap /mnt/snap

# Giải thích:
# - Snapshot là bản copy tại thời điểm
# - Hữu ích cho backup
# - Chỉ lưu thay đổi (copy-on-write)
```

## Phần 6: Bài Tập Thực Hành

### Bài 1: Thiết Lập Storage cho Web Server
```bash
# 1. Tạo LVM structure
sudo pvcreate /dev/sdb
sudo vgcreate web_vg /dev/sdb
sudo lvcreate -n www_lv -L 5G web_vg

# 2. Tạo filesystem và mount
sudo mkfs.ext4 /dev/web_vg/www_lv
sudo mkdir /var/www
sudo mount /dev/web_vg/www_lv /var/www

# Mục đích:
# - Quản lý storage cho web content
# - Dễ dàng mở rộng khi cần
# - Tách biệt với system storage
```

### Bài 2: Backup Strategy
```bash
# 1. Tạo snapshot trước khi update
sudo lvcreate -s -n www_snap -L 1G /dev/web_vg/www_lv

# 2. Test updates
sudo rsync -av /source/ /var/www/

# 3. Rollback nếu cần
sudo lvconvert --merge /dev/web_vg/www_snap

# Mục đích:
# - Bảo vệ dữ liệu
# - Test changes an toàn
# - Rollback nhanh chóng
```

## Best Practices

1. **Planning:**
   - Dự tính growth
   - Chọn đúng partition scheme
   - Để space cho snapshot

2. **Security:**
   - Regular backups
   - Monitor disk usage
   - Verify filesystem integrity

3. **Performance:**
   - Align partitions
   - Choose appropriate filesystem
   - Monitor I/O stats

4. **Maintenance:**
   - Regular filesystem checks
   - Clean up unused snapshots
   - Monitor LVM metadata

