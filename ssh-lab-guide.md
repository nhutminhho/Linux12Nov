# Lab: Thiết lập SSH Key Authentication với User Ansible

## Mục tiêu
- Tạo user ansible trên tất cả các máy (control, node1, node2)
- Thiết lập SSH key authentication cho user ansible
- Cấu hình SSH để kết nối không cần password
- Đảm bảo kết nối SSH an toàn giữa control và các node

## Môi trường
- 3 máy ảo chạy AlmaLinux 9 (đã được tạo bằng Vagrant)
- Control: 192.168.56.10
- Node1: 192.168.56.11
- Node2: 192.168.56.12

## Phần 1: Tạo và Cấu hình User Ansible

### 1.1. Đăng nhập vào máy Control
```bash
vagrant ssh control
```

### 1.2. Tạo user ansible trên tất cả các máy
Thực hiện các lệnh sau trên cả 3 máy (control, node1, node2):

```bash
# Tạo user ansible
sudo useradd ansible

# Đặt password cho user ansible (ví dụ: ansible123)
sudo passwd ansible

# Thêm user ansible vào group wheel (sudo)
sudo usermod -aG wheel ansible
```

Giải thích:
- `useradd ansible`: Tạo mới một user với tên "ansible"
- `passwd ansible`: Thiết lập mật khẩu cho user ansible
- `usermod -aG wheel ansible`: Thêm user ansible vào group wheel để có quyền sudo

### 1.3. Cấu hình sudo không yêu cầu password
```bash
# Tạo file cấu hình sudo cho group wheel
echo "%wheel  ALL=(ALL)       NOPASSWD: ALL" | sudo tee /etc/sudoers.d/wheel
```

Giải thích:
- File được tạo trong `/etc/sudoers.d/` sẽ được include vào cấu hình sudo
- `%wheel`: Áp dụng cho tất cả users trong group wheel
- `NOPASSWD: ALL`: Cho phép thực thi tất cả lệnh sudo mà không cần nhập password

## Phần 2: Thiết lập SSH Key

### 2.1. Tạo SSH Key trên Control
```bash
# Chuyển sang user ansible
sudo su - ansible

# Tạo SSH key
ssh-keygen -t rsa -b 2048
```

Giải thích các tham số:
- `-t rsa`: Sử dụng thuật toán mã hóa RSA
- `-b 2048`: Độ dài key 2048 bit (cân bằng giữa bảo mật và hiệu năng)

Output của lệnh `ssh-keygen`:
```plaintext
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ansible/.ssh/id_rsa): [Press Enter]
Enter passphrase (empty for no passphrase): [Press Enter]
Enter same passphrase again: [Press Enter]
```

### 2.2. Kiểm tra SSH Key đã tạo
```bash
ls -la ~/.ssh/
```

Output sẽ hiển thị:
- `id_rsa`: Private key (quyền 600)
- `id_rsa.pub`: Public key (quyền 644)

## Phần 3: Copy SSH Key đến Các Node

### 3.1. Sử dụng ssh-copy-id
```bash
# Copy key đến node1
ssh-copy-id ansible@node1

# Copy key đến node2
ssh-copy-id ansible@node2
```

Giải thích:
- `ssh-copy-id`: Công cụ tự động copy public key và cấu hình đúng permissions
- Tự động thêm key vào file `~/.ssh/authorized_keys` trên node đích
- Tự động thiết lập đúng permissions cho thư mục và file

### 3.2. Cấu hình SSH Client
```bash
# Tạo và cấu hình file config
cat << EOF > ~/.ssh/config
Host node1
    HostName node1
    User ansible
    IdentityFile ~/.ssh/id_rsa
    StrictHostKeyChecking no

Host node2
    HostName node2
    User ansible
    IdentityFile ~/.ssh/id_rsa
    StrictHostKeyChecking no
EOF

# Đặt quyền đúng cho file config
chmod 600 ~/.ssh/config
```

Giải thích cấu hình:
- `Host`: Alias cho máy đích
- `HostName`: Tên host hoặc IP của máy đích
- `User`: User mặc định khi SSH
- `IdentityFile`: Đường dẫn đến private key
- `StrictHostKeyChecking no`: Không kiểm tra host key khi lần đầu kết nối

## Phần 4: Kiểm tra Kết nối

### 4.1. Test SSH Connection
```bash
# Test kết nối đến node1
ssh node1 "hostname; whoami; pwd"

# Test kết nối đến node2
ssh node2 "hostname; whoami; pwd"
```

### 4.2. Test Sudo Access
```bash
# Test sudo trên node1
ssh node1 "sudo ls -la /root"

# Test sudo trên node2
ssh node2 "sudo ls -la /root"
```

## Phần 5: Xử lý Sự cố

### 5.1. Kiểm tra Permissions
```bash
# Kiểm tra permissions thư mục .ssh
ls -la ~/.ssh

# Permissions chuẩn:
# drwx------ (700) ~/.ssh
# -rw------- (600) ~/.ssh/id_rsa
# -rw-r--r-- (644) ~/.ssh/id_rsa.pub
# -rw------- (600) ~/.ssh/config
```

### 5.2. Kiểm tra Log
```bash
# Xem log SSH trên node
sudo tail -f /var/log/secure
```

### 5.3. Debug SSH Connection
```bash
# Thử kết nối với chế độ verbose
ssh -vv node1
```

## Best Practices & Bảo mật

1. Permissions quan trọng:
   - Thư mục home: 750 hoặc nghiêm ngặt hơn
   - Thư mục .ssh: 700
   - Private key (id_rsa): 600
   - Public key (id_rsa.pub): 644
   - authorized_keys: 600
   - config: 600

2. Không sử dụng passphrase cho key trong môi trường automation
   - Trong môi trường production nên cân nhắc sử dụng passphrase
   - Sử dụng ssh-agent nếu dùng passphrase

3. Backup các key quan trọng
   - Lưu private key ở nơi an toàn
   - Có thể tạo key backup để phòng trường hợp mất key chính

4. Giám sát và Logging
   - Theo dõi log SSH thường xuyên
   - Cấu hình log rotation cho /var/log/secure