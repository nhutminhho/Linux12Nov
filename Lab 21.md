# Giải Thích Chi Tiết: Quản Lý Storage Từ Căn Bản

## 1. Khái Niệm Cơ Bản

### 1.1. Ổ Đĩa Vật Lý
```bash
# Kiểm tra ổ đĩa
lsblk
fdisk -l
```

**Giải thích:**
- Ổ đĩa được đặt tên như sda, sdb, sdc,...
- "sd" = SCSI disk hoặc SATA disk
- Số thứ tự a, b, c để phân biệt các ổ

**Tại sao cần hiểu điều này?**
1. Để không nhầm lẫn khi thao tác
2. Tránh xóa nhầm ổ đĩa hệ thống
3. Hiểu quy tắc đặt tên trong Linux

### 1.2. Phân Vùng (Partition)
```bash
# Ví dụ về phân vùng
/dev/sda1  # Phân vùng thứ nhất của ổ sda
/dev/sda2  # Phân vùng thứ hai của ổ sda
```

**Giải thích cấu trúc:**
- /dev = thư mục chứa các thiết bị
- sda = ổ đĩa đầu tiên
- 1,2,3 = số thứ tự phân vùng

**Tại sao cần phân vùng?**
1. Tổ chức dữ liệu hợp lý
2. Bảo vệ dữ liệu (nếu một phân vùng hỏng)
3. Quản lý không gian hiệu quả

## 2. Kiểu Phân Vùng

### 2.1. MBR (Master Boot Record)
```bash
# Tạo MBR partition table
sudo parted /dev/sdb mklabel msdos
```

**Đặc điểm MBR:**
- Tối đa 4 phân vùng chính
- Giới hạn dung lượng 2TB
- Được dùng trong hệ thống cũ

**Tại sao vẫn dùng MBR?**
1. Tương thích ngược với hệ thống cũ
2. Đơn giản, dễ hiểu
3. Đủ dùng cho ổ đĩa nhỏ

### 2.2. GPT (GUID Partition Table)
```bash
# Tạo GPT partition table
sudo parted /dev/sdc mklabel gpt
```

**Ưu điểm GPT:**
- Hỗ trợ đến 128 phân vùng
- Không giới hạn dung lượng thực tế
- Có backup partition table

**Khi nào dùng GPT?**
1. Ổ đĩa lớn hơn 2TB
2. Cần nhiều phân vùng
3. Hệ thống UEFI mới

## 3. LVM (Logical Volume Management)

### 3.1. Tại Sao Cần LVM?
**Giải thích:**
- Quản lý không gian linh hoạt
- Thay đổi kích thước dễ dàng
- Tạo snapshot dữ liệu

### 3.2. Các Thành Phần LVM
```bash
# 1. Physical Volume (PV)
sudo pvcreate /dev/sdb
# Tại sao: Chuẩn bị ổ đĩa cho LVM

# 2. Volume Group (VG)
sudo vgcreate data_vg /dev/sdb
# Tại sao: Gom nhiều PV thành một nhóm

# 3. Logical Volume (LV)
sudo lvcreate -n data_lv -L 5G data_vg
# Tại sao: Tạo "phân vùng ảo" từ VG
```

**Quy trình tạo LVM:**
1. Tạo PV: 
   - Chuẩn bị ổ đĩa vật lý
   - Thêm metadata LVM
   - Chia thành physical extents

2. Tạo VG:
   - Gom các PV
   - Tạo pool chung
   - Dễ quản lý

3. Tạo LV:
   - Phân chia VG
   - Linh hoạt kích thước
   - Mount và sử dụng

## 4. Filesystem

### 4.1. Tại Sao Cần Filesystem?
```bash
# Tạo filesystem
sudo mkfs.ext4 /dev/data_vg/data_lv
```

**Giải thích:**
1. Tổ chức dữ liệu:
   - Quản lý file và thư mục
   - Phân quyền truy cập
   - Theo dõi metadata

2. Hiệu năng:
   - Tối ưu đọc/ghi
   - Cache và buffer
   - Journaling

### 4.2. Chọn Loại Filesystem

**1. ext4:**
```bash
sudo mkfs.ext4 /dev/data_vg/data_lv
```
- Ổn định, phổ biến
- Phục hồi dữ liệu tốt
- Phù hợp mọi mục đích

**2. xfs:**
```bash
sudo mkfs.xfs /dev/data_vg/data_lv
```
- Hiệu năng cao
- Mở rộng online tốt
- Phù hợp file lớn

## 5. Mount và Sử Dụng

### 5.1. Mount Thủ Công
```bash
# Tạo mount point
sudo mkdir /mnt/data

# Mount
sudo mount /dev/data_vg/data_lv /mnt/data
```

**Tại sao cần mount point?**
1. Điểm truy cập vào filesystem
2. Tổ chức cấu trúc thư mục
3. Quản lý quyền truy cập

### 5.2. Mount Tự Động
```bash
# Thêm vào /etc/fstab
/dev/data_vg/data_lv  /mnt/data  ext4  defaults  0  2
```

**Giải thích từng trường:**
1. Device: đường dẫn thiết bị
2. Mountpoint: điểm mount
3. Filesystem: loại filesystem
4. Options: tùy chọn mount
5. Dump: backup flag
6. Pass: thứ tự fsck

## 6. Bài Tập Thực Hành

### Bài Tập 1: Tạo Storage Cho Web Server
```bash
# Yêu cầu:
1. Tạo LVM 10GB cho web data
2. Sử dụng ext4 filesystem
3. Mount vào /var/www
4. Cấu hình auto mount

# Tại sao làm vậy?
- Quản lý dữ liệu web riêng
- Dễ mở rộng khi cần
- Backup độc lập
```

### Các Lưu Ý Quan Trọng
1. **Backup trước khi thay đổi**
2. **Kiểm tra kỹ lệnh**
3. **Document mọi thay đổi**
4. **Test trên môi trường thử nghiệm**

