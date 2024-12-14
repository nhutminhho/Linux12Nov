# Lab Thực Hành: Bảo Mật Hệ Thống Linux

## Phần 1: Chuẩn Bị Hệ Thống

### 1.1. Kiểm Tra và Cập Nhật Hệ Thống
```bash
# 1. Kiểm tra phiên bản hệ điều hành
cat /etc/redhat-release

# 2. Cập nhật toàn bộ hệ thống
sudo dnf update -y

# 3. Kiểm tra các package đã cài đặt
rpm -qa > /root/installed-packages.txt

# Giải thích:
# - Cập nhật hệ thống để vá lỗi bảo mật
# - Lưu danh sách package để audit
# - Theo dõi những thay đổi trong hệ thống
```

## Phần 2: Bảo Mật SSH

### Lab 2.1: Cấu Hình SSH An Toàn
```bash
# 1. Backup cấu hình SSH
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# 2. Chỉnh sửa cấu hình
sudo nano /etc/ssh/sshd_config

# Thêm/sửa các dòng sau:
Port 2222                     # Đổi port mặc định
PermitRootLogin no           # Không cho phép root login
PasswordAuthentication no    # Chỉ cho phép key authentication
MaxAuthTries 3              # Giới hạn số lần thử
Protocol 2                  # Chỉ dùng SSHv2

# 3. Khởi động lại SSH
sudo systemctl restart sshd

# Giải thích:
# - Đổi port để tránh scan tự động
# - Cấm root login giảm rủi ro
# - Key auth an toàn hơn password
# - Giới hạn brute force attempts
```

## Phần 3: Cấu Hình Firewall

### Lab 3.1: Thiết Lập Firewalld
```bash
# 1. Kiểm tra và khởi động firewalld
sudo systemctl status firewalld
sudo systemctl enable --now firewalld

# 2. Cấu hình rules cơ bản
# Đóng tất cả ports trừ những port cần thiết
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-port=2222/tcp
sudo firewall-cmd --permanent --remove-service=telnet
sudo firewall-cmd --reload

# 3. Kiểm tra cấu hình
sudo firewall-cmd --list-all

# Giải thích:
# - Firewall là tuyến phòng thủ đầu tiên
# - Chỉ mở những port thực sự cần thiết
# - Permanent flag để lưu cấu hình vĩnh viễn
```

## Phần 4: Quản Lý User và Password

### Lab 4.1: Thiết Lập Chính Sách Password
```bash
# 1. Chỉnh sửa cấu hình PAM
sudo nano /etc/security/pwquality.conf

# Thêm các cấu hình:
minlen = 12                # Độ dài tối thiểu
minclass = 3               # Số loại ký tự khác nhau
maxrepeat = 3             # Số lần lặp tối đa
enforce_for_root          # Áp dụng cả cho root

# 2. Cấu hình thời gian hết hạn
sudo nano /etc/login.defs

PASS_MAX_DAYS   90
PASS_MIN_DAYS   7
PASS_WARN_AGE   7

# Giải thích:
# - Password mạnh ngăn chặn brute force
# - Thay đổi định kỳ giảm rủi ro
# - Cảnh báo trước khi hết hạn
```

## Phần 5: Audit và Monitoring

### Lab 5.1: Cấu Hình Audit System
```bash
# 1. Cài đặt auditd
sudo dnf install audit -y

# 2. Cấu hình audit rules
sudo nano /etc/audit/rules.d/audit.rules

# Thêm rules:
-w /etc/passwd -p wa -k password-file
-w /etc/shadow -p wa -k password-file
-w /var/log/faillog -p wa -k logins
-w /var/log/lastlog -p wa -k logins

# 3. Khởi động lại audit
sudo systemctl restart auditd

# Giải thích:
# - Audit giúp phát hiện thay đổi
# - Theo dõi files quan trọng
# - Lưu log cho phân tích
```

## Phần 6: System Hardening

### Lab 6.1: Tắt Các Services Không Cần Thiết
```bash
# 1. Liệt kê services
systemctl list-unit-files --type=service

# 2. Tắt services không cần thiết
sudo systemctl disable --now cups.service
sudo systemctl disable --now avahi-daemon.service
sudo systemctl disable --now bluetooth.service

# 3. Xóa packages không cần thiết
sudo dnf remove telnet rsh tftp

# Giải thích:
# - Giảm surface attack
# - Tiết kiệm tài nguyên
# - Loại bỏ services có rủi ro
```

## Phần 7: Bài Tập Thực Hành

### Bài 1: Thiết Lập Secure Server
```bash
# 1. Update và hardening cơ bản
sudo dnf update -y
sudo dnf install -y fail2ban

# 2. Cấu hình fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local

# Thêm cấu hình:
[sshd]
enabled = true
port = 2222
filter = sshd
logpath = /var/log/secure
maxretry = 3
bantime = 3600

# Giải thích:
# - Fail2ban bảo vệ khỏi brute force
# - Tự động block IP tấn công
# - Customizable rules
```

### Bài 2: Monitoring Setup
```bash
# 1. Cài đặt monitoring tools
sudo dnf install -y rkhunter lynis

# 2. Chạy system scan
sudo rkhunter --check
sudo lynis audit system

# 3. Analyze kết quả
less /var/log/lynis.log
less /var/log/rkhunter.log

# Giải thích:
# - Scan định kỳ phát hiện malware
# - Kiểm tra lỗ hổng hệ thống
# - Recommendations để cải thiện
```

## Best Practices

1. **Regular Maintenance:**
   - Cập nhật hệ thống thường xuyên
   - Kiểm tra logs định kỳ
   - Backup dữ liệu quan trọng

2. **Access Control:**
   - Principle of least privilege
   - Strong password policy
   - Regular user audit

3. **Network Security:**
   - Firewall configuration
   - Service hardening
   - Network monitoring

4. **Monitoring:**
   - System auditing
   - Log analysis
   - Intrusion detection

