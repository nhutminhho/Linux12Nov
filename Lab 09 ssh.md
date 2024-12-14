# Lab: Kết Nối SSH từ Windows đến Máy Ảo AlmaLinux

## Phần 1: Chuẩn Bị Môi Trường

### 1.1. Yêu Cầu Hệ Thống
- Máy Windows 10/11
- VirtualBox hoặc VMware Workstation
- File ISO AlmaLinux (download từ almalinux.org)
- PuTTY hoặc Windows Terminal
- WinSCP (cho việc truyền file)

### 1.2. Cài Đặt VirtualBox và AlmaLinux
1. Cài đặt VirtualBox:
   - Tải VirtualBox từ virtualbox.org
   - Cài đặt với các tùy chọn mặc định
   - Cài đặt Extension Pack nếu cần

2. Tạo máy ảo AlmaLinux:
   - Nhấn "New" trong VirtualBox
   - Đặt tên: AlmaLinux
   - Type: Linux
   - Version: Red Hat (64-bit)
   - Memory: 2048MB
   - Create virtual hard disk: 20GB

3. Cấu hình Network:
   - Chọn máy ảo → Settings
   - Network → Adapter 1
   - Attached to: Bridged Adapter
   - Chọn card mạng thật của máy

### 1.3. Cài Đặt AlmaLinux
1. Mount file ISO và khởi động máy ảo
2. Trong quá trình cài đặt:
   - Chọn ngôn ngữ: English
   - Cấu hình timezone
   - Tạo root password
   - Tạo user thường (ví dụ: sshuser)

## Phần 2: Cấu Hình AlmaLinux

### 2.1. Cấu Hình Mạng
```bash
# Đăng nhập vào AlmaLinux với user root
# Kiểm tra IP
ip addr show

# Cập nhật hệ thống
dnf update -y

# Cài đặt net-tools
dnf install net-tools -y

# Xem IP chi tiết
ifconfig
```

### 2.2. Cài Đặt và Cấu Hình SSH Server
```bash
# Cài đặt OpenSSH server
dnf install openssh-server -y

# Kiểm tra trạng thái
systemctl status sshd

# Khởi động SSH
systemctl start sshd
systemctl enable sshd

# Mở port SSH trong firewall
firewall-cmd --permanent --add-service=ssh
firewall-cmd --reload
```

### 2.3. Cấu Hình SSH An Toàn
```bash
# Sao lưu file cấu hình
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# Chỉnh sửa file cấu hình
nano /etc/ssh/sshd_config

# Thêm/sửa các dòng sau:
PermitRootLogin no
MaxAuthTries 3
PasswordAuthentication yes

# Khởi động lại SSH
systemctl restart sshd
```

## Phần 3: Chuẩn Bị Windows

### 3.1. Cài Đặt PuTTY
1. Tải PuTTY:
   - Vào putty.org
   - Tải phiên bản mới nhất cho Windows
   - Chạy file cài đặt

2. Cài đặt WinSCP:
   - Tải từ winscp.net
   - Cài đặt với tùy chọn mặc định

### 3.2. Cài Đặt Windows Terminal (Tùy chọn)
1. Mở Microsoft Store
2. Tìm "Windows Terminal"
3. Nhấn Install

## Phần 4: Kết Nối SSH

### 4.1. Kết Nối với PuTTY
1. Mở PuTTY
2. Điền thông tin:
   - Host Name: IP của máy ảo AlmaLinux
   - Port: 22
   - Connection type: SSH
3. Lưu session (tùy chọn):
   - Điền tên trong Saved Sessions
   - Click Save

4. Click Open để kết nối
5. Chấp nhận security alert
6. Đăng nhập với user đã tạo

### 4.2. Kết Nối với Windows Terminal
```powershell
# Mở Windows Terminal
# Sử dụng lệnh SSH
ssh username@ip_address
```

### 4.3. Truyền File với WinSCP
1. Mở WinSCP
2. Tạo New Site:
   - File Protocol: SFTP
   - Host name: IP máy ảo
   - Port: 22
   - User name: username
3. Click Login
4. Kéo thả file để truyền

## Phần 5: Bảo Mật Nâng Cao

### 5.1. Tạo SSH Key trên Windows
```powershell
# Trong Windows Terminal hoặc PowerShell
ssh-keygen -t rsa -b 4096

# Key được lưu trong:
# C:\Users\your_username\.ssh\id_rsa
# C:\Users\your_username\.ssh\id_rsa.pub
```

### 5.2. Copy Key sang AlmaLinux
#### Cách 1: Sử dụng ssh-copy-id trong Git Bash
```bash
# Cài Git for Windows nếu chưa có
# Mở Git Bash
ssh-copy-id username@ip_address
```

#### Cách 2: Copy thủ công
1. Mở file id_rsa.pub trên Windows
2. Copy nội dung
3. Trên AlmaLinux:
```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
echo "copied_key" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### 5.3. Tắt Xác Thực Password
```bash
# Trên AlmaLinux
sudo nano /etc/ssh/sshd_config

# Sửa/thêm dòng
PasswordAuthentication no

# Khởi động lại SSH
sudo systemctl restart sshd
```

## Phần 6: Xử Lý Sự Cố

### 6.1. Kiểm Tra Kết Nối
```bash
# Trên Windows
ping ip_address

# Kiểm tra port SSH
telnet ip_address 22
```

### 6.2. Các Lỗi Thường Gặp

1. Không Ping được máy ảo:
   - Kiểm tra cấu hình mạng trong VirtualBox
   - Kiểm tra firewall trên Windows
   - Kiểm tra firewall trên AlmaLinux

2. Không kết nối được SSH:
```bash
# Trên AlmaLinux
systemctl status sshd
ss -tulnp | grep 22
firewall-cmd --list-all
```

3. Lỗi Permission:
```bash
# Kiểm tra quyền .ssh
ls -la ~/.ssh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

## Phần 7: Tối Ưu Hóa

### 7.1. Cấu Hình PuTTY
1. Lưu Session:
   - Điền thông tin kết nối
   - Đặt tên trong Saved Sessions
   - Click Save

2. Tùy chỉnh giao diện:
   - Window → Appearance
   - Chọn font và size
   - Điều chỉnh màu sắc

3. Cấu hình Auto-login:
   - Connection → Data
   - Auto-login username
   - Connection → SSH → Auth
   - Browse đến private key

### 7.2. Tạo SSH Config trên Windows
```powershell
# Tạo file config trong .ssh
notepad C:\Users\username\.ssh\config

# Thêm nội dung
Host almalinux
    HostName ip_address
    User username
    Port 22
    IdentityFile ~/.ssh/id_rsa
```

## Phần 8: Bài Tập Thực Hành

### Bài 1: Thiết Lập Cơ Bản
1. Cài đặt máy ảo AlmaLinux
2. Cấu hình mạng bridge
3. Cài đặt SSH server
4. Kết nối từ Windows

### Bài 2: Bảo Mật
1. Tạo SSH key
2. Thiết lập key authentication
3. Tắt đăng nhập root
4. Đổi port SSH

### Bài 3: Truyền File
1. Cấu hình WinSCP
2. Upload/Download files
3. Thiết lập bookmark
4. Tự động sync thư mục

