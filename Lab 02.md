# Bài Thực Hành Regular Expression và Shell Script

## Lab 1: Tìm Hiểu Regular Expression với grep

### Bài 1.1: Tìm kiếm cơ bản với grep
```bash
# Bước 1: Tạo file dữ liệu test
cat > users.txt << 'EOF'
admin123
user_test
TEST_admin
root_user
Root_admin
test123
admin_test
EOF

# Bước 2: Thực hiện tìm kiếm
# Tìm các dòng có chứa "test"
grep test users.txt

# Tìm kiếm không phân biệt hoa thường
grep -i test users.txt

# Hiển thị số dòng
grep -n test users.txt

Kết quả mong đợi:
- user_test
- admin_test
```

### Bài 1.2: Tìm kiếm nâng cao trong file hệ thống
```bash
# Bước 1: Tìm kiếm trong /etc/passwd
# Tìm các user có shell là /bin/bash
grep "/bin/bash" /etc/passwd

# Tìm các user không có quyền đăng nhập
grep "nologin" /etc/passwd

# Tìm và loại trừ các user không có quyền đăng nhập
grep -v "nologin" /etc/passwd

# Bước 2: Tìm kiếm với điều kiện đặc biệt
# Tìm dòng bắt đầu bằng root
grep ^root /etc/passwd

# Tìm các dòng trống
grep ^$ /etc/passwd
```

### Bài 1.3: Tìm kiếm nhiều mẫu
```bash
# Tạo file config test
cat > config.txt << 'EOF'
server1=active
server2=inactive
database1=running
database2=stopped
web1=active
web2=maintenance
EOF

# Tìm kiếm các server đang active hoặc maintenance
egrep 'active|maintenance' config.txt

# Đếm số lượng server active
grep -c "active" config.txt
```

## Lab 2: Làm Việc với Metacharacters

### Bài 2.1: Sử dụng ký tự *
```bash
# Bước 1: Tạo cấu trúc thư mục test
mkdir -p ~/test/{logs,config,data}
touch ~/test/logs/app.log
touch ~/test/logs/error.log
touch ~/test/logs/access.log
touch ~/test/config/app.conf
touch ~/test/data/data.db

# Bước 2: Thực hành với *
# Liệt kê tất cả file .log
ls ~/test/logs/*.log

# Liệt kê tất cả file bắt đầu bằng app
ls ~/test/**/app.*
```

### Bài 2.2: Sử dụng ký tự ?
```bash
# Bước 1: Tạo các file test
touch file1.txt file2.txt file10.txt file20.txt

# Bước 2: Sử dụng ? để tìm kiếm
# Tìm file có 1 ký tự số
ls file?.txt

# Kết quả mong đợi:
# file1.txt file2.txt
```

### Bài 2.3: Sử dụng []
```bash
# Bước 1: Tạo files test
touch test{1..5}.txt
touch testA.txt testB.txt testC.txt

# Bước 2: Tìm kiếm với []
# Tìm file test có số từ 1-3
ls test[1-3].txt

# Tìm file test có chữ A hoặc B
ls test[AB].txt
```

## Lab 3: Thực Hành Shell Script

### Bài 3.1: Script Cơ Bản
```bash
# Bước 1: Tạo script đầu tiên
cat > hello.sh << 'EOF'
#!/bin/bash
# My first script
echo "Hello World!"
echo "Today is $(date)"
echo "User logged in: $USER"
EOF

# Bước 2: Cấp quyền và chạy
chmod +x hello.sh
./hello.sh
```

### Bài 3.2: Script Tương Tác với Người Dùng
```bash
# Tạo script nhập liệu
cat > user_input.sh << 'EOF'
#!/bin/bash
echo "Nhập tên của bạn:"
read name
echo "Nhập tuổi của bạn:"
read age
echo "Xin chào $name!"
echo "Bạn đã $age tuổi"
EOF

chmod +x user_input.sh
./user_input.sh
```

### Bài 3.3: Script Quản Lý File
```bash
# Script tạo backup
cat > backup.sh << 'EOF'
#!/bin/bash

# Tạo thư mục backup nếu chưa tồn tại
backup_dir="$HOME/backup"
mkdir -p $backup_dir

# Tạo tên file backup với timestamp
timestamp=$(date +%Y%m%d_%H%M%S)
backup_file="$backup_dir/backup_$timestamp.tar.gz"

# Thực hiện backup thư mục home
tar -czf $backup_file $HOME/test

# Thông báo kết quả
if [ $? -eq 0 ]; then
    echo "Backup thành công: $backup_file"
else
    echo "Backup thất bại!"
fi
EOF

chmod +x backup.sh
./backup.sh
```

## Bài Tập Thực Hành Tổng Hợp

### Bài Tập 1: Tìm và Xử Lý Log
```bash
# Tạo file log mẫu
cat > access.log << 'EOF'
192.168.1.100 - - [10/Dec/2023:13:55:36 +0700] "GET /index.html HTTP/1.1" 200 2326
192.168.1.101 - - [10/Dec/2023:13:56:12 +0700] "GET /images/logo.png HTTP/1.1" 404 289
192.168.1.102 - - [10/Dec/2023:13:56:36 +0700] "POST /login HTTP/1.1" 500 1234
192.168.1.100 - - [10/Dec/2023:13:57:00 +0700] "GET /admin HTTP/1.1" 403 1500
EOF

# Yêu cầu:
# 1. Tìm tất cả các request lỗi (status code 4xx hoặc 5xx)
# 2. Đếm số lượng request từ mỗi IP
# 3. Lưu kết quả vào file report.txt
```

### Bài Tập 2: Script Giám Sát Hệ Thống
```bash
# Tạo script monitor.sh
cat > monitor.sh << 'EOF'
#!/bin/bash

# Kiểm tra CPU
cpu_usage=$(top -bn1 | grep "Cpu(s)" | sed "s/.*, *\([0-9.]*\)%* id.*/\1/" | awk '{print 100 - $1}')

# Kiểm tra RAM
mem_usage=$(free | grep Mem | awk '{print $3/$2 * 100.0}')

# Kiểm tra disk
disk_usage=$(df -h / | tail -n 1 | awk '{print $5}' | sed 's/%//')

# Tạo báo cáo
echo "=== Báo cáo hệ thống ==="
echo "CPU Usage: ${cpu_usage}%"
echo "Memory Usage: ${mem_usage}%"
echo "Disk Usage: ${disk_usage}%"

# Cảnh báo nếu vượt ngưỡng
if (( $(echo "$cpu_usage > 80" | bc -l) )); then
    echo "CẢNH BÁO: CPU usage cao!"
fi
EOF

chmod +x monitor.sh
```

### Bài Tập 3: Tự Động Hóa Quản Lý User
```bash
# Script tạo và quản lý user
cat > user_manage.sh << 'EOF'
#!/bin/bash

function add_user() {
    read -p "Nhập tên user: " username
    read -s -p "Nhập password: " password
    echo
    useradd $username
    echo "$password" | passwd --stdin $username
    echo "Đã tạo user $username"
}

function delete_user() {
    read -p "Nhập tên user cần xóa: " username
    userdel -r $username
    echo "Đã xóa user $username"
}

# Menu
echo "1. Thêm user"
echo "2. Xóa user"
echo "3. Thoát"
read -p "Chọn chức năng (1-3): " choice

case $choice in
    1) add_user ;;
    2) delete_user ;;
    3) exit ;;
    *) echo "Lựa chọn không hợp lệ" ;;
esac
EOF

chmod +x user_manage.sh
```

## Ghi Chú và Mẹo Thực Hành

1. Kiểm tra Syntax Script
```bash
# Sử dụng shellcheck để kiểm tra script
shellcheck script.sh
```

2. Debug Script
```bash
# Thêm -x để debug
bash -x script.sh
```

3. Xử Lý Lỗi Thường Gặp
- Permission denied: Kiểm tra quyền thực thi
- Command not found: Kiểm tra PATH
- Syntax error: Sử dụng shellcheck

4. Tối Ưu Hóa Script
- Sử dụng functions cho code tái sử dụng
- Thêm comments để giải thích code
- Xử lý lỗi và timeout

## Kết Luận

Sau khi hoàn thành bài lab, sinh viên sẽ:
1. Thành thạo sử dụng grep và regular expressions
2. Hiểu và áp dụng được metacharacters
3. Có thể viết và debug shell scripts
4. Biết cách tự động hóa các tác vụ quản trị hệ thống

