# Lab Thực Hành: Lập Lịch với Crontab

## Phần 1: Tìm Hiểu Crontab

### 1.1. Tại Sao Cần Crontab?
- **Mục đích:**
  - Tự động hóa công việc định kỳ
  - Giảm công việc thủ công
  - Đảm bảo công việc được thực hiện đúng giờ
  - Quản lý tài nguyên hiệu quả

- **Ưu điểm so với at:**
  - Thực hiện công việc lặp lại
  - Dễ quản lý và chỉnh sửa
  - Linh hoạt trong cấu hình thời gian

### 1.2. Chuẩn Bị Môi Trường
```bash
# Kiểm tra cài đặt
rpm -qa | grep cronie

# Cài đặt nếu chưa có
sudo dnf install cronie -y

# Khởi động service
sudo systemctl start crond
sudo systemctl enable crond

# Giải thích:
# - cronie: Package quản lý cron jobs
# - crond: Daemon thực thi các jobs
# - enable: Đảm bảo service chạy sau khi reboot
```

## Phần 2: Thực Hành Cơ Bản

### Lab 2.1: Tạo Cron Job Đầu Tiên
```bash
# 1. Mở crontab editor
crontab -e

# 2. Thêm job đơn giản
# Chạy mỗi phút
* * * * * echo "Cron is running at $(date)" >> /tmp/cron_test.log

# Giải thích format:
# * * * * * command
# | | | | |
# | | | | +-- Ngày trong tuần (0-6) (Chủ nhật = 0)
# | | | +---- Tháng (1-12)
# | | +------ Ngày trong tháng (1-31)
# | +-------- Giờ (0-23)
# +---------- Phút (0-59)
```

### Lab 2.2: Kiểm Tra và Quản Lý Jobs
```bash
# Xem danh sách jobs
crontab -l

# Xóa tất cả jobs
crontab -r

# Giải thích:
# - -l: List tất cả jobs
# - -r: Remove tất cả jobs
# - Cẩn thận với -r vì sẽ xóa hết không khôi phục được
```

## Phần 3: Thực Hành Nâng Cao

### Lab 3.1: Lập Lịch Backup Tự Động
```bash
# 1. Tạo script backup
cat > backup.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/backup/$(date +%Y%m%d)"
mkdir -p $BACKUP_DIR
tar -czf $BACKUP_DIR/home_backup.tar.gz /home
find /backup -mtime +7 -delete
echo "Backup completed at $(date)" >> /var/log/backup.log
EOF

chmod +x backup.sh

# 2. Thêm vào crontab
# Chạy lúc 2 giờ sáng mỗi ngày
0 2 * * * /path/to/backup.sh

# Giải thích:
# - 0 2 * * *: 2:00 AM mỗi ngày
# - Tự động xóa backup cũ hơn 7 ngày
# - Log kết quả để theo dõi
```

### Lab 3.2: Monitoring System
```bash
# 1. Tạo script monitor
cat > monitor.sh << 'EOF'
#!/bin/bash
# Check CPU usage
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}')
# Check memory
MEM_USAGE=$(free -m | awk 'NR==2{printf "%.2f%%\n", $3*100/$2}')
# Check disk
DISK_USAGE=$(df -h / | awk 'NR==2{print $5}')

echo "$(date) - CPU: $CPU_USAGE%, MEM: $MEM_USAGE, DISK: $DISK_USAGE" >> /var/log/system_monitor.log

# Alert if usage is high
if [ ${CPU_USAGE%.*} -gt 80 ]; then
    echo "High CPU Alert!" | mail -s "System Alert" admin@example.com
fi
EOF

chmod +x monitor.sh

# 2. Lập lịch chạy mỗi 5 phút
*/5 * * * * /path/to/monitor.sh

# Giải thích:
# - */5: Mỗi 5 phút
# - Kiểm tra và log thông số hệ thống
# - Gửi alert khi có vấn đề
```

## Phần 4: Các Pattern Thời Gian Phổ Biến

### 4.1. Ví Dụ và Giải Thích
```bash
# Mỗi phút
* * * * * command

# Mỗi giờ (phút 0)
0 * * * * command

# Mỗi ngày lúc 2:30 AM
30 2 * * * command

# Mỗi Chủ Nhật lúc 3 AM
0 3 * * 0 command

# Mỗi 15 phút
*/15 * * * * command

# Giải thích:
# - *: Mọi giá trị
# - */n: Mỗi n đơn vị
# - x-y: Từ x đến y
# - x,y: Tại x và y
```

## Phần 5: Best Practices và Troubleshooting

### 5.1. Logging và Monitoring
```bash
# 1. Kiểm tra logs
tail -f /var/log/cron

# 2. Kiểm tra mail system
tail -f /var/log/maillog

# Giải thích:
# - Cron gửi output qua email
# - Quan trọng để debug
# - Nên redirect output trong scripts
```

### 5.2. Security
```bash
# Kiểm tra quyền truy cập
ls -l /etc/cron.allow
ls -l /etc/cron.deny

# Giải thích:
# - Kiểm soát ai được dùng cron
# - Tăng cường bảo mật
# - Ngăn chặn lạm dụng tài nguyên
```

### 5.3. Tips Quan Trọng
1. **Đường dẫn:**
   - Luôn dùng đường dẫn tuyệt đối
   - Set PATH trong script
   - Tránh lỗi command not found

2. **Output:**
   - Redirect stdout và stderr
   - Log đầy đủ thông tin
   - Dễ dàng debug

3. **Testing:**
   - Test script riêng trước
   - Verify timing
   - Check resource usage

