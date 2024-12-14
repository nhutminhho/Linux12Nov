# Lab Thực Hành: Lập Lịch Công Việc (Job Scheduling)

## Phần 1: Chuẩn Bị Môi Trường

### 1.1. Kiểm Tra và Cài Đặt at
```bash
# Kiểm tra at đã cài đặt chưa
rpm -qa | grep at

# Cài đặt nếu chưa có
sudo dnf install at -y

# Khởi động service
sudo systemctl start atd
sudo systemctl enable atd

# Giải thích:
# - at cần thiết cho one-time job scheduling
# - atd daemon phải chạy để thực thi các jobs
# - enable để service tự động chạy khi khởi động
```

### 1.2. Xác Nhận Quyền Truy Cập
```bash
# Kiểm tra các file kiểm soát truy cập
ls -l /etc/at.deny
ls -l /etc/at.allow

# Giải thích:
# - at.deny: Liệt kê users không được dùng at
# - at.allow: Liệt kê users được phép dùng at
# - Nếu không có file nào: mọi user đều dùng được
```

## Phần 2: Thực Hành với at Command

### Lab 2.1: Lập Lịch Job Đơn Giản
```bash
# Tạo một job đơn giản
at now + 2 minutes
at> echo "Hello from future" > /tmp/at_test.txt
at> date >> /tmp/at_test.txt
at> <Ctrl+D>

# Giải thích:
# - now + 2 minutes: Chạy sau 2 phút
# - Ctrl+D để kết thúc nhập lệnh
# - Job sẽ tạo file và ghi thời gian vào đó
```

### Lab 2.2: Làm Việc với at Jobs
```bash
# Xem danh sách jobs
atq

# Xem chi tiết một job
at -c [job_number]

# Xóa một job
atrm [job_number]

# Giải thích:
# - atq: Hiển thị queue của at jobs
# - at -c: Xem nội dung và biến môi trường của job
# - atrm: Xóa job khỏi queue
```

## Phần 3: Thực Hành với Script Files

### Lab 3.1: Tạo Script để Lập Lịch
```bash
# Tạo script backup đơn giản
cat > backup.sh << 'EOF'
#!/bin/bash
DATE=$(date +%Y%m%d)
tar -czf /tmp/backup_$DATE.tar.gz /home/user/documents
echo "Backup completed at $(date)" >> /tmp/backup.log
EOF

# Cấp quyền thực thi
chmod +x backup.sh

# Lập lịch chạy script
at -f backup.sh midnight

# Giải thích:
# - Script tạo backup với timestamp
# - midnight: Chạy vào lúc 12 giờ đêm
# - -f: Chỉ định file script cần chạy
```

## Phần 4: Quản Lý Quyền Truy Cập

### Lab 4.1: Cấu Hình at.allow và at.deny
```bash
# Tạo user test
sudo useradd testuser

# Hạn chế quyền sử dụng at
sudo echo "testuser" >> /etc/at.deny

# Thử lập lịch với testuser
su - testuser
at now + 1 hour
# Sẽ bị từ chối

# Giải thích:
# - at.deny kiểm soát ai không được dùng at
# - Việc này giúp bảo mật hệ thống
# - Chỉ cho phép users đáng tin cậy lập lịch
```

## Phần 5: Monitoring và Troubleshooting

### Lab 5.1: Kiểm Tra Logs
```bash
# Xem log của at jobs
sudo tail -f /var/log/cron

# Kiểm tra status của atd
systemctl status atd

# Giải thích:
# - Logs giúp theo dõi thực thi jobs
# - Phát hiện lỗi nếu có
# - Xác nhận jobs đã chạy thành công
```

## Phần 6: Bài Tập Thực Hành

### Bài 1: Backup Tự Động
```bash
# Tạo script backup
cat > daily_backup.sh << 'EOF'
#!/bin/bash
# Tạo backup của thư mục home
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d_%H%M%S)
mkdir -p $BACKUP_DIR
tar -czf $BACKUP_DIR/home_$DATE.tar.gz /home
find $BACKUP_DIR -mtime +7 -delete
EOF

# Lập lịch chạy hàng ngày lúc 2 giờ sáng
at -f daily_backup.sh 2:00AM tomorrow
```

### Bài 2: System Maintenance
```bash
# Script dọn dẹp hệ thống
cat > cleanup.sh << 'EOF'
#!/bin/bash
# Xóa các file tạm
find /tmp -type f -atime +7 -delete
# Xóa các log cũ
find /var/log -name "*.gz" -mtime +30 -delete
# Thông báo kết quả
echo "Cleanup completed at $(date)" >> /var/log/cleanup.log
EOF

# Lập lịch chạy vào cuối tuần
at -f cleanup.sh 'midnight sunday'
```

## Ghi Chú Quan Trọng

### 1. Best Practices
- Luôn test scripts trước khi lập lịch
- Sử dụng đường dẫn tuyệt đối trong scripts
- Log kết quả thực thi
- Kiểm tra quyền truy cập files/directories

### 2. Các Định Dạng Thời Gian
```
now + N minutes/hours/days/weeks
HH:MM today/tomorrow
midnight/noon
MM/DD/YYYY
```

### 3. Troubleshooting
- Kiểm tra atd service đang chạy
- Xem logs trong /var/log/cron
- Verify file permissions
- Check user permissions trong at.allow/at.deny

### 4. Security Considerations
- Hạn chế quyền sử dụng at
- Review scripts trước khi lập lịch
- Monitor job execution
- Regular audit của scheduled jobs

