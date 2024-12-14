# Thực Hành Cơ Bản: Quản Lý Storage Trên Linux

## Bước 1: Tìm Hiểu Ổ Đĩa Hiện Có

### 1.1. Kiểm tra ổ đĩa trong hệ thống
```bash
# Xem danh sách ổ đĩa
sudo fdisk -l
```

**Giải thích kết quả:**
```
Disk /dev/sda: 20 GiB
# sda là ổ đĩa đầu tiên, 20GB
# Thường là ổ cài hệ điều hành

Disk /dev/sdb: 10 GiB
# sdb là ổ đĩa thứ hai, 10GB
# Đây là ổ đĩa mới thêm vào
```

### 1.2. Xem cấu trúc phân vùng
```bash
# Xem cấu trúc dạng cây
lsblk
```

**Kết quả mẫu:**
```
sda           # Ổ đĩa thứ nhất
├─sda1        # Phân vùng 1
└─sda2        # Phân vùng 2
sdb           # Ổ đĩa thứ hai
```

## Bước 2: Tạo Phân Vùng Đơn Giản

### 2.1. Sử dụng fdisk để tạo phân vùng
```bash
# Mở fdisk cho ổ sdb
sudo fdisk /dev/sdb
```

**Các lệnh trong fdisk:**
```
n - tạo phân vùng mới
p - xem danh sách phân vùng
w - lưu và thoát
q - thoát không lưu
```

### 2.2. Tạo phân vùng mới
```bash
# Trong fdisk:
n     # Tạo phân vùng mới
p     # Chọn phân vùng primary
1     # Số thứ tự phân vùng
Enter # Chấp nhận sector đầu mặc định
+5G   # Kích thước 5GB
w     # Lưu thay đổi
```

## Bước 3: Tạo Filesystem

### 3.1. Format phân vùng với ext4
```bash
# Format phân vùng mới tạo
sudo mkfs.ext4 /dev/sdb1
```

**Giải thích quá trình:**
- mkfs = make filesystem
- ext4 = loại filesystem phổ biến
- /dev/sdb1 = phân vùng cần format

### 3.2. Kiểm tra filesystem
```bash
# Xem thông tin filesystem
sudo blkid /dev/sdb1
```

## Bước 4: Mount Filesystem

### 4.1. Tạo thư mục mount point
```bash
# Tạo thư mục để mount
sudo mkdir /mnt/data

# Giải thích:
- /mnt là thư mục chuẩn để mount
- data là tên tự chọn
```

### 4.2. Mount thủ công
```bash
# Mount phân vùng
sudo mount /dev/sdb1 /mnt/data

# Kiểm tra
df -h
```

### 4.3. Cấu hình auto mount
```bash
# Backup file fstab
sudo cp /etc/fstab /etc/fstab.backup

# Thêm vào fstab
echo "/dev/sdb1 /mnt/data ext4 defaults 0 2" | sudo tee -a /etc/fstab
```

## Bước 5: Thử Nghiệm Sử Dụng

### 5.1. Tạo file test
```bash
# Tạo file test
sudo touch /mnt/data/test.txt
sudo chmod 777 /mnt/data/test.txt
echo "Test nội dung" > /mnt/data/test.txt
```

### 5.2. Kiểm tra quyền và nội dung
```bash
# Xem quyền file
ls -l /mnt/data/test.txt

# Xem nội dung
cat /mnt/data/test.txt
```

## Bài Tập Thực Hành

### Bài Tập 1: Tạo Storage Đơn Giản
1. Mục tiêu:
   - Thêm ổ cứng mới 5GB
   - Tạo một phân vùng đầy đủ
   - Format với ext4
   - Mount vào /mnt/mydata

2. Các bước thực hiện:
```bash
# Thêm ổ cứng trong VirtualBox
# Kiểm tra ổ mới
sudo fdisk -l

# Tạo phân vùng
sudo fdisk /dev/sdc
# Nhập các lệnh: n, p, 1, Enter, Enter, w

# Format
sudo mkfs.ext4 /dev/sdc1

# Mount
sudo mkdir /mnt/mydata
sudo mount /dev/sdc1 /mnt/mydata
```

### Bài Tập 2: Test Performance
1. Test ghi:
```bash
# Test tốc độ ghi
dd if=/dev/zero of=/mnt/mydata/test bs=1M count=100
```

2. Test đọc:
```bash
# Xóa cache
sudo sh -c "sync && echo 3 > /proc/sys/vm/drop_caches"

# Test tốc độ đọc
dd if=/mnt/mydata/test of=/dev/null bs=1M
```

## Tips và Lưu Ý

1. **An Toàn Dữ Liệu:**
   - Luôn backup trước khi thay đổi
   - Kiểm tra kỹ lệnh trước khi thực hiện
   - Mount đúng điểm

2. **Hiệu Năng:**
   - Chọn filesystem phù hợp
   - Kiểm tra alignment
   - Monitor IO

3. **Troubleshooting:**
   ```bash
   # Kiểm tra mount
   mount | grep sdb1
   
   # Xem log
   dmesg | tail
   
   # Kiểm tra filesystem
   sudo fsck /dev/sdb1
   ```

