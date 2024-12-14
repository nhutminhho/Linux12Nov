# Thực Hành Cấu Hình và Sử Dụng Sudo

## Mục Tiêu
- Hiểu cách hoạt động của sudo
- Cấu hình quyền sudo cho user
- Thực hành sử dụng sudo

## Chuẩn Bị
1. Hệ thống Linux (AlmaLinux/CentOS)
2. Tài khoản root
3. Terminal để thực hành

## Lab 1: Tạo User và Cấu Hình Sudo Cơ Bản

### Tình huống
Công ty cần tạo tài khoản cho admin mới (helpdesk) và cấp quyền sudo.

### Các bước thực hiện

```bash
# 1. Đăng nhập với quyền root
su -

# 2. Tạo user mới
useradd -m -s /bin/bash helpdesk
passwd helpdesk
# Đặt mật khẩu: Helpdesk123@

# 3. Xem nội dung file sudoers
visudo
# hoặc
cat /etc/sudoers
```

### Thêm quyền sudo cho user
```bash
# Cách 1: Thêm user vào group wheel
usermod -aG wheel helpdesk

# Cách 2: Chỉnh sửa file sudoers
visudo
# Thêm dòng sau:
helpdesk    ALL=(ALL)       ALL

# Kiểm tra kết quả
id helpdesk
groups helpdesk
```

## Lab 2: Kiểm Tra Quyền Sudo

### Tình huống
Kiểm tra xem user helpdesk có thể thực hiện các lệnh sudo hay không.

### Các bước thực hiện
```bash
# 1. Chuyển sang user helpdesk
su - helpdesk

# 2. Thử các lệnh sudo
# Xem thông tin hệ thống
sudo systemctl status sshd

# Xem nội dung file cấu hình
sudo cat /etc/ssh/sshd_config

# Kiểm tra quyền sudo
sudo -l
```

## Lab 3: Cấu Hình Sudo Không Cần Mật Khẩu

### Tình huống
Cấu hình cho helpdesk thực hiện một số lệnh sudo mà không cần nhập mật khẩu.

### Các bước thực hiện
```bash
# 1. Mở file sudoers
sudo visudo

# 2. Thêm dòng sau để cho phép thực hiện không cần mật khẩu
helpdesk    ALL=(ALL)       NOPASSWD: /usr/bin/systemctl status *

# 3. Kiểm tra
su - helpdesk
sudo systemctl status sshd  # Không cần nhập mật khẩu
```

## Lab 4: Cấu Hình Sudo Cho Lệnh Cụ Thể

### Tình huống
Cho phép helpdesk chỉ được thực hiện một số lệnh nhất định với sudo.

### Các bước thực hiện
```bash
# 1. Mở file sudoers
sudo visudo

# 2. Thêm quyền giới hạn
helpdesk    ALL=(ALL)       /usr/bin/systemctl status *, /usr/bin/less /var/log/*

# 3. Kiểm tra
su - helpdesk
sudo systemctl status sshd      # Được phép
sudo less /var/log/messages     # Được phép
sudo nano /etc/hosts           # Không được phép
```

## Lab 5: Tạo Alias Trong Sudoers

### Tình huống
Tạo nhóm lệnh để dễ quản lý quyền sudo.

### Các bước thực hiện
```bash
# 1. Mở file sudoers
sudo visudo

# 2. Tạo Command Alias
# Thêm các dòng sau:
Cmnd_Alias SYSTEM_STATUS = /usr/bin/systemctl status *, /usr/bin/top, /usr/bin/df
Cmnd_Alias LOG_VIEWERS = /usr/bin/less /var/log/*, /usr/bin/tail -f /var/log/*

# 3. Áp dụng cho user
helpdesk    ALL=(ALL)       SYSTEM_STATUS, LOG_VIEWERS

# 4. Kiểm tra
su - helpdesk
sudo systemctl status sshd  # OK
sudo top                    # OK
sudo tail -f /var/log/messages  # OK
```

## Bài Tập Thực Hành

### Bài tập 1: Cấu hình cơ bản
1. Tạo user support với mật khẩu Support123@
2. Thêm user support vào group wheel
3. Kiểm tra quyền sudo của user support

### Bài tập 2: Cấu hình nâng cao
1. Tạo Command Alias NETWORK_CMDS bao gồm:
   - /usr/bin/ping
   - /usr/bin/traceroute
   - /usr/bin/netstat
2. Cho phép user support sử dụng NETWORK_CMDS không cần mật khẩu

## Xử Lý Lỗi Thường Gặp

### 1. Lỗi Cú Pháp trong sudoers
```bash
# Kiểm tra cú pháp
visudo -c

# Nếu có lỗi, sửa lại file
sudo visudo
```

### 2. Lỗi Permission Denied
```bash
# Kiểm tra quyền sudo
sudo -l

# Kiểm tra trong file sudoers
sudo cat /etc/sudoers | grep username
```

### 3. Lỗi Command Not Found
```bash
# Kiểm tra đường dẫn đầy đủ của lệnh
which command_name

# Sử dụng đường dẫn đầy đủ trong sudoers
```

## Ghi Chú Quan Trọng

1. **Nguyên tắc an toàn**
   - Luôn sử dụng visudo để sửa file sudoers
   - Hạn chế cấp quyền NOPASSWD
   - Chỉ cấp quyền cần thiết cho user

2. **Kiểm tra sau khi cấu hình**
   - Luôn test quyền sau khi thay đổi
   - Giữ một phiên root để khắc phục sự cố
   - Backup file sudoers trước khi sửa

3. **Định dạng file sudoers**
   - User/Group    Host=(User:Group)    Commands
   - % đại diện cho group
   - ALL là wildcard cho tất cả
   - NOPASSWD bỏ qua yêu cầu mật khẩu

