# Lab Thực Hành: Giám Sát và Quản Lý Hệ Thống Linux

## Phần 1: Theo Dõi Log Hệ Thống

### Lab 1.1: Xem và Phân Tích Log
```bash
# 1. Xem toàn bộ log từ lúc khởi động
journalctl

# 2. Xem log theo thời gian thực
journalctl -f

# 3. Xem log kernel
journalctl -k

# Giải thích:
- journalctl: công cụ quản lý log hiện đại
- -f: follow mode, xem log realtime
- -k: chỉ xem log kernel
```

### Lab 1.2: Tìm Log Cụ Thể
```bash
# Xem log SSH
journalctl | grep sshd

# Xem log boot
journalctl -b

# Giải thích:
- grep: lọc thông tin cần thiết
- -b: chỉ xem log của lần boot hiện tại
- Giúp debug vấn đề cụ thể
```

## Phần 2: Giám Sát Tài Nguyên

### Lab 2.1: Kiểm Tra Memory
```bash
# Xem thông tin RAM
free -h

# Theo dõi memory realtime
vmstat 2

# Giải thích:
- -h: hiển thị theo định dạng con người đọc được
- 2: cập nhật mỗi 2 giây
- Quan trọng để phát hiện memory leak
```

### Lab 2.2: Giám Sát CPU
```bash
# Cài đặt sysstat
sudo apt install sysstat

# Xem thống kê CPU
sar -u 1 5

# Giải thích:
- sar: công cụ thu thập và báo cáo
- -u: thống kê CPU
- 1 5: 5 lần, mỗi lần cách 1 giây
```

## Phần 3: Quản Lý Process

### Lab 3.1: Tìm và Quản Lý Process
```bash
# Tìm PID của process
pidof httpd

# Kill process
kill -9 PID

# Giải thích:
- pidof: tìm ID của process
- -9: signal buộc dừng
- Cẩn thận với kill -9
```

### Lab 3.2: Kiểm Tra File Mở
```bash
# Xem file đang mở của user
lsof -u username

# Xem kết nối mạng
lsof -i :80

# Giải thích:
- lsof: list open files
- -u: theo user
- -i: theo port
```

## Phần 4: Cấu Hình Giới Hạn Hệ Thống

### Lab 4.1: Quản Lý Ulimit
```bash
# Xem giới hạn hiện tại
ulimit -a

# Tăng giới hạn file mở
sudo nano /etc/security/limits.conf

# Thêm dòng:
* soft nofile 65535
* hard nofile 65535

# Giải thích:
- soft: giới hạn mềm
- hard: giới hạn cứng
- nofile: số file mở tối đa
```

### Lab 4.2: Kernel Parameters
```bash
# Xem tham số kernel
sysctl -a

# Thay đổi tham số
sudo nano /etc/sysctl.conf

# Áp dụng thay đổi
sudo sysctl -p

# Giải thích:
- sysctl: quản lý tham số kernel
- -p: load từ file cấu hình
- Cẩn thận khi thay đổi
```

## Phần 5: Bài Tập Thực Hành

### Bài 1: Theo Dõi Hệ Thống
```bash
# 1. Tạo script monitor
cat > monitor.sh << 'EOF'
#!/bin/bash
while true; do
    echo "=== $(date) ==="
    echo "Memory Usage:"
    free -h
    echo "CPU Usage:"
    top -bn1 | head -n 5
    sleep 5
done
EOF

chmod +x monitor.sh

# Giải thích:
- Script chạy liên tục
- Thu thập thông tin quan trọng
- Cập nhật mỗi 5 giây
```

### Bài 2: Phân Tích Log
```bash
# 1. Tìm lỗi đăng nhập
journalctl -u sshd | grep "Failed"

# 2. Tạo báo cáo
journalctl --since "1 hour ago" > ~/report.txt

# Giải thích:
- Tìm các lần đăng nhập thất bại
- Tạo báo cáo cho phân tích
- Lưu để theo dõi
```

## Tips Quan Trọng

### 1. Giám Sát Hiệu Quả
```bash
# CPU và Memory
top
htop
glances

# Disk Usage
df -h
iostat

# Network
netstat
ss
```

### 2. Xử Lý Sự Cố
```bash
# Kiểm tra log
tail -f /var/log/syslog

# Xem process ngốn tài nguyên
ps aux --sort=-%mem
ps aux --sort=-%cpu

# Kill process treo
killall -9 process_name
```

### 3. Best Practices
1. **Backup Trước Khi Thay Đổi:**
   ```bash
   cp /etc/sysctl.conf /etc/sysctl.conf.backup
   ```

2. **Monitor Regular:**
   ```bash
   # Tạo cron job
   */5 * * * * /root/monitor.sh >> /var/log/monitor.log
   ```

3. **Alert Setup:**
   - Cấu hình cảnh báo khi:
     + RAM > 80%
     + CPU > 90%
     + Disk > 85%

