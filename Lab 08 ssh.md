# Lab Thực Hành: SSH từ Cơ Bản đến Nâng Cao

## Phần 1: Chuẩn Bị Môi Trường

### Yêu Cầu
- 2 máy chủ Linux (có thể dùng VirtualBox)
  - Server1: 192.168.1.10 (server)
  - Server2: 192.168.1.11 (client)
- Kết nối mạng giữa hai máy
- Quyền root hoặc sudo

### Bước 1: Kiểm tra và Cài đặt SSH
```bash
# Trên cả hai máy
# Kiểm tra SSH đã cài chưa
rpm -qa | grep ssh

# Cài đặt nếu chưa có
sudo dnf install openssh-server openssh-clients -y

# Kiểm tra trạng thái service
sudo systemctl status sshd

# Khởi động service nếu chưa chạy
sudo systemctl start sshd
sudo systemctl enable sshd
```

### Bước 2: Cấu hình Firewall
```bash
# Cho phép SSH qua firewall
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --reload

# Kiểm tra port 22 đã mở
sudo ss -tulnp | grep 22
```

## Phần 2: SSH Cơ Bản

### Bước 1: Tạo User Test
```bash
# Trên server1
sudo useradd -m sshtest
sudo passwd sshtest
# Đặt mật khẩu: SSHtest123@

# Trên server2
sudo useradd -m sshtest
sudo passwd sshtest
# Đặt mật khẩu: SSHtest123@
```

### Bước 2: Kết Nối SSH Đầu Tiên
```bash
# Từ server2, kết nối đến server1
ssh sshtest@192.168.1.10

# Chấp nhận fingerprint khi được hỏi
# Nhập mật khẩu khi được yêu cầu

# Kiểm tra kết nối
whoami
hostname
pwd
```

## Phần 3: Tăng Cường Bảo Mật SSH

### Bước 1: Sao Lưu Cấu Hình
```bash
# Trên server1
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup
```

### Bước 2: Thay Đổi Port và Tắt Root Login
```bash
# Chỉnh sửa file cấu hình
sudo nano /etc/ssh/sshd_config

# Thay đổi các dòng sau:
Port 2222
PermitRootLogin no
MaxAuthTries 3
PasswordAuthentication yes

# Khởi động lại service
sudo systemctl restart sshd

# Cập nhật firewall
sudo firewall-cmd --permanent --remove-service=ssh
sudo firewall-cmd --permanent --add-port=2222/tcp
sudo firewall-cmd --reload
```

### Bước 3: Kiểm Tra Cấu Hình Mới
```bash
# Từ server2, thử kết nối với port mới
ssh -p 2222 sshtest@192.168.1.10

# Thử kết nối với root (sẽ bị từ chối)
ssh -p 2222 root@192.168.1.10
```

## Phần 4: Xác Thực Key-Based

### Bước 1: Tạo SSH Key
```bash
# Trên server2
ssh-keygen -t rsa -b 4096
# Nhấn Enter để sử dụng vị trí mặc định
# Có thể đặt passphrase hoặc để trống

# Xem key đã tạo
ls -l ~/.ssh/
cat ~/.ssh/id_rsa.pub
```

### Bước 2: Copy Key sang Server
```bash
# Từ server2
ssh-copy-id -i ~/.ssh/id_rsa.pub -p 2222 sshtest@192.168.1.10

# Kiểm tra file authorized_keys trên server1
ssh -p 2222 sshtest@192.168.1.10
cat ~/.ssh/authorized_keys
```

### Bước 3: Tắt Xác Thực Password
```bash
# Trên server1
sudo nano /etc/ssh/sshd_config

# Thay đổi
PasswordAuthentication no

# Khởi động lại service
sudo systemctl restart sshd
```

## Phần 5: Truyền File Qua SSH

### Bước 1: Sử Dụng SCP
```bash
# Tạo file test trên server2
echo "Test file content" > testfile.txt

# Copy file từ server2 sang server1
scp -P 2222 testfile.txt sshtest@192.168.1.10:~/

# Copy file từ server1 về server2
scp -P 2222 sshtest@192.168.1.10:~/testfile.txt ./downloaded_file.txt
```

### Bước 2: Sử Dụng SFTP
```bash
# Kết nối SFTP
sftp -P 2222 sshtest@192.168.1.10

# Các lệnh SFTP cơ bản
pwd         # Xem thư mục hiện tại
ls          # Liệt kê file
put file    # Upload file
get file    # Download file
bye         # Thoát
```

## Phần 6: Cấu Hình SSH Nâng Cao

### Bước 1: Giới Hạn Truy Cập
```bash
# Trên server1
sudo nano /etc/ssh/sshd_config

# Thêm các dòng
AllowUsers sshtest
Protocol 2
ClientAliveInterval 300
ClientAliveCountMax 2
```

### Bước 2: Tùy Chỉnh Banner
```bash
# Tạo banner file
sudo nano /etc/ssh/banner
# Thêm nội dung warning

# Cập nhật sshd_config
sudo nano /etc/ssh/sshd_config
Banner /etc/ssh/banner

# Khởi động lại service
sudo systemctl restart sshd
```

## Phần 7: Bài Tập Thực Hành

### Bài 1: Cấu Hình Basic
1. Tạo user mới tên devops
2. Cấu hình SSH cho phép user devops
3. Thay đổi port SSH sang 2345
4. Kiểm tra kết nối

### Bài 2: Cấu Hình Security
1. Tạo SSH key cho user devops
2. Thiết lập key-based authentication
3. Tắt password authentication
4. Cấu hình timeout 5 phút

### Bài 3: Cấu Hình Advanced
1. Giới hạn SSH chỉ từ một dải IP
2. Tạo custom banner
3. Giới hạn số lần đăng nhập sai
4. Log tất cả các kết nối SSH

## Xử Lý Sự Cố

### 1. Không Kết Nối Được
```bash
# Kiểm tra service
sudo systemctl status sshd

# Kiểm tra port
sudo ss -tulnp | grep sshd

# Kiểm tra firewall
sudo firewall-cmd --list-all

# Kiểm tra SELinux
sudo semanage port -l | grep ssh
```

### 2. Lỗi Permission
```bash
# Kiểm tra quyền file
ls -l ~/.ssh
ls -l ~/.ssh/authorized_keys

# Sửa quyền nếu cần
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

### 3. Lỗi Key
```bash
# Xác minh key
ssh-keygen -l -f ~/.ssh/id_rsa.pub

# Debug kết nối
ssh -vv user@host
```

## Ghi Chú Quan Trọng

1. **Bảo Mật**
   - Luôn đổi port mặc định
   - Sử dụng key thay vì password
   - Giới hạn user được phép SSH
   - Cập nhật thường xuyên

2. **Performance**
   - Sử dụng Compression nếu mạng chậm
   - Cấu hình KeepAlive phù hợp
   - Tối ưu cipher list

3. **Monitoring**
   - Kiểm tra log thường xuyên
   - Theo dõi các attempt thất bại
   - Cấu hình fail2ban nếu cần

