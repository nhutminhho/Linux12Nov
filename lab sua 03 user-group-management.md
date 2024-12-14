# Lab Thực Hành: Script Quản Lý Hệ Thống

## 1. Script Hoàn Chỉnh

```bash
#!/bin/bash

# Function hiển thị disk space
display_disk_space() {
    echo "=== Disk Space Usage ==="
    df -h | grep '^/dev'
    echo "======================="
    read -p "Press Enter to continue..."
}

# Function hiển thị users đang login
display_logged_users() {
    echo "=== Logged On Users ==="
    who
    echo "======================="
    read -p "Press Enter to continue..."
}

# Function hiển thị memory usage
display_memory_usage() {
    echo "=== Memory Usage ==="
    free -h
    echo "==================="
    read -p "Press Enter to continue..."
}

# Function reboot system
reboot_system() {
    echo -n "Are you sure you want to reboot? (yes/no): "
    read answer
    case "$answer" in
        yes|YES)
            echo "System will reboot in 5 seconds..."
            sleep 5
            sudo reboot
            ;;
        *)
            echo "Reboot cancelled"
            sleep 2
            ;;
    esac
}

# Function poweroff system
poweroff_system() {
    echo -n "Are you sure you want to shutdown? (yes/no): "
    read answer
    case "$answer" in
        yes|YES)
            echo "System will shutdown in 5 seconds..."
            sleep 5
            sudo poweroff
            ;;
        *)
            echo "Shutdown cancelled"
            sleep 2
            ;;
    esac
}

# Main menu loop
while true; do
    clear
    echo "=== System Admin Menu ==="
    echo "1. Display disk space"
    echo "2. Display logged on users"
    echo "3. Display memory usage"
    echo "4. Reboot system"
    echo "5. Poweroff system"
    echo "6. Exit program"
    echo "========================"
    
    read -p "Enter option: " choice
    
    case $choice in
        1) display_disk_space ;;
        2) display_logged_users ;;
        3) display_memory_usage ;;
        4) reboot_system ;;
        5) poweroff_system ;;
        6)
            echo -n "Are you sure you want to exit? (yes/no): "
            read answer
            if [[ "$answer" == "yes" || "$answer" == "YES" ]]; then
                echo "Goodbye! Have a nice day!"
                sleep 2
                exit 0
            fi
            ;;
        *)
            echo "Invalid option!"
            sleep 2
            ;;
    esac
done
```

## 2. Giải Thích Chi Tiết

### 2.1. Cấu Trúc Functions

```bash
# Template function
function_name() {
    # Hiển thị tiêu đề
    echo "=== Title ==="
    
    # Thực hiện lệnh chính
    command
    
    # Hiển thị đường kẻ
    echo "============="
    
    # Dừng để đọc kết quả
    read -p "Press Enter to continue..."
}
```

**Lý do:**
1. Tách chức năng thành functions:
   - Dễ bảo trì và sửa đổi
   - Tái sử dụng code
   - Code rõ ràng, dễ đọc

2. Format hiển thị:
   - Tiêu đề rõ ràng
   - Đường kẻ phân cách
   - Dừng để đọc kết quả

### 2.2. Xử Lý Xác Nhận

```bash
echo -n "Are you sure? (yes/no): "
read answer
case "$answer" in
    yes|YES)
        # Thực hiện hành động
        ;;
    *)
        # Hủy hành động
        ;;
esac
```

**Lý do:**
1. Hỏi xác nhận:
   - Tránh thao tác nhầm
   - Cho phép hủy hành động
   - An toàn cho hệ thống

2. Sử dụng case:
   - Xử lý nhiều trường hợp
   - Code rõ ràng
   - Dễ thêm options

### 2.3. Display Functions

```bash
# Disk space
df -h | grep '^/dev'

# Logged users
who

# Memory usage
free -h
```

**Lý do:**
1. df -h:
   - -h: hiển thị dễ đọc (human-readable)
   - grep '^/dev': chỉ hiện physical disks

2. who:
   - Hiển thị users đang login
   - Thông tin về terminal và thời gian

3. free -h:
   - Hiển thị memory usage
   - -h: format dễ đọc

### 2.4. System Control Functions

```bash
# Reboot/Poweroff
sleep 5
sudo reboot/poweroff
```

**Lý do:**
1. Delay 5 giây:
   - Cho phép hủy lệnh
   - Thời gian lưu thông tin
   - User experience tốt hơn

2. Sử dụng sudo:
   - Đảm bảo quyền thực thi
   - Bảo mật hệ thống

## 3. Tips và Lưu Ý

### 3.1. Error Handling
```bash
# Kiểm tra quyền root
if [ "$(id -u)" -ne 0 ]; then
    echo "This script must be run as root"
    exit 1
fi
```

### 3.2. User Interface
```bash
# Clear screen trước khi hiện menu
clear

# Sleep sau mỗi thông báo
sleep 2
```

### 3.3. Logging
```bash
# Log các hoạt động quan trọng
logger "System reboot initiated by $(whoami)"
```

## 4. Best Practices

1. **Input Validation:**
   - Kiểm tra input hợp lệ
   - Thông báo lỗi rõ ràng
   - Cho phép sửa lỗi

2. **User Experience:**
   - Menu rõ ràng
   - Thông báo đầy đủ
   - Thời gian chờ hợp lý

3. **Bảo Mật:**
   - Kiểm tra quyền
   - Xác nhận các lệnh nguy hiểm
   - Log hoạt động

4. **Maintainability:**
   - Comment code đầy đủ
   - Tổ chức code rõ ràng
   - Dễ mở rộng chức năng

