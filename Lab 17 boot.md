# Lab Thực Hành: Quản Lý System Targets và Kernel

## Phần 1: Tìm Hiểu System Targets

### 1.1. Tại Sao Cần System Targets?
- **Mục đích:**
  - Quản lý trạng thái hệ thống
  - Kiểm soát services chạy khi khởi động
  - Xác định môi trường làm việc (GUI/CLI)
  - Tối ưu hóa tài nguyên hệ thống

### 1.2. Targets và Runlevels Tương Ứng
```bash
# Kiểm tra targets hiện có
ls -l /usr/lib/systemd/system/runlevel*.target

# Các target chính:
# poweroff.target    = Runlevel 0 (Tắt máy)
# rescue.target      = Runlevel 1 (Single mode)
# multi-user.target  = Runlevel 3 (Command line)
# graphical.target   = Runlevel 5 (GUI)
# reboot.target      = Runlevel 6 (Khởi động lại)

# Giải thích:
# - Targets linh hoạt hơn runlevels
# - Có thể chạy song song
# - Dễ quản lý dependencies
```

## Phần 2: Thực Hành với System Targets

### Lab 2.1: Kiểm Tra và Chuyển Đổi Target
```bash
# 1. Xem target hiện tại
systemctl get-default

# 2. Liệt kê tất cả targets
systemctl list-units --type=target

# 3. Chuyển sang multi-user target
sudo systemctl isolate multi-user.target

# Giải thích:
# - get-default: Xem target mặc định khi boot
# - list-units: Xem tất cả targets đang chạy
# - isolate: Chuyển sang target khác ngay lập tức
```

### Lab 2.2: Cấu Hình Default Target
```bash
# 1. Đặt multi-user làm mặc định
sudo systemctl set-default multi-user.target

# 2. Xác nhận thay đổi
systemctl get-default

# 3. Kiểm tra symbolic link
ls -l /etc/systemd/system/default.target

# Giải thích:
# - set-default: Thay đổi target khi boot
# - Tạo symbolic link đến target mong muốn
# - Ảnh hưởng sau khi reboot
```

## Phần 3: Thực Hành với Kernel

### Lab 3.1: Kiểm Tra Thông Tin Kernel
```bash
# 1. Xem phiên bản kernel hiện tại
uname -r

# 2. Xem tất cả thông tin kernel
uname -a

# 3. Liệt kê các kernel đã cài
dnf list installed kernel*

# Giải thích:
# - Kernel quản lý phần cứng và tài nguyên
# - Nhiều kernel có thể cài đồng thời
# - Kernel mới nhất thường là mặc định
```

### Lab 3.2: Cập Nhật Kernel
```bash
# 1. Kiểm tra kernel updates
sudo dnf check-update kernel

# 2. Cài đặt kernel mới
sudo dnf update kernel -y

# 3. Kiểm tra sau khi cài
rpm -qa | grep kernel

# Giải thích:
# - Giữ kernel cũ để backup
# - Update thêm kernel entry vào GRUB
# - Reboot để sử dụng kernel mới
```

## Phần 4: Quản Lý GRUB Boot Loader

### Lab 4.1: Cấu Hình GRUB
```bash
# 1. Xem cấu hình GRUB
cat /etc/default/grub

# 2. Chỉnh sửa timeout
sudo nano /etc/default/grub
# Sửa GRUB_TIMEOUT=10

# 3. Cập nhật cấu hình
sudo grub2-mkconfig -o /boot/grub2/grub.cfg

# Giải thích:
# - GRUB quản lý boot menu
# - Cho phép chọn kernel khi boot
# - Timeout quyết định thời gian hiển thị menu
```

## Phần 5: Bài Tập Thực Hành

### Bài 1: Chuyển Đổi Giữa CLI và GUI
```bash
# 1. Kiểm tra target hiện tại
systemctl get-default

# 2. Cài đặt GNOME (nếu chưa có)
sudo dnf groupinstall "Server with GUI"

# 3. Chuyển đổi giữa các modes
sudo systemctl isolate graphical.target
sudo systemctl isolate multi-user.target

# Mục đích:
# - Học cách chuyển đổi môi trường
# - Hiểu sự khác biệt về resources
# - Thực hành quản lý targets
```

### Bài 2: Quản Lý Kernel
```bash
# 1. Tạo backup của cấu hình hiện tại
sudo cp /boot/grub2/grub.cfg /boot/grub2/grub.cfg.backup

# 2. Cài đặt kernel-devel
sudo dnf install kernel-devel kernel-headers

# 3. Kiểm tra không gian đĩa
df -h /boot

# Mục đích:
# - Hiểu cách quản lý nhiều kernels
# - Học cách backup cấu hình
# - Chuẩn bị cho development
```

## Phần 6: Troubleshooting

### 6.1. Xử Lý Lỗi Boot
```bash
# 1. Boot vào rescue target
systemctl isolate rescue.target

# 2. Kiểm tra logs
journalctl -xb

# 3. Khôi phục target mặc định
systemctl set-default multi-user.target
```

### 6.2. Xử Lý Lỗi Kernel
```bash
# 1. Boot vào kernel cũ
# Chọn kernel cũ trong GRUB menu

# 2. Remove kernel có vấn đề
sudo dnf remove kernel-$(uname -r)

# 3. Reinstall kernel nếu cần
sudo dnf install kernel
```

## Best Practices

1. **System Target:**
   - Dùng multi-user.target cho servers
   - Backup trước khi thay đổi
   - Test kỹ trước khi set-default

2. **Kernel Management:**
   - Giữ ít nhất 2 kernel versions
   - Kiểm tra space trong /boot
   - Test kernel mới trước khi xóa cũ

3. **Boot Management:**
   - Cấu hình GRUB timeout hợp lý
   - Backup GRUB configuration
   - Duy trì rescue boot options

