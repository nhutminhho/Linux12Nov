# Script System Admin Menu

## I. Phân tích yêu cầu

### 1. Chức năng cần có:
- Hiển thị disk space
- Hiển thị users đang logged on
- Hiển thị memory usage
- Reboot system
- Poweroff system
- Exit program

### 2. Yêu cầu đặc biệt:
- Mỗi chức năng là một function
- Confirm trước khi reboot/poweroff
- Exit có confirm và chào tạm biệt

## II. Giải pháp chi tiết

### 1. Script hoàn chỉnh
```bash
#!/bin/bash

# File: system_admin.sh
# Mục đích: Script quản lý hệ thống với menu

# Định nghĩa màu sắc
GREEN='\033[0;32m'
RED='\033[0;31m'
BLUE='\033[0;34m'
NC='\033[0m'  # No Color

# Function hiển thị disk space
display_disk_space() {
    echo -e "${BLUE}=== Disk Space Information ===${NC}"
    df -h | grep '^/dev'  # Chỉ hiển thị physical disks
}

# Function hiển thị users đang logged on
display_logged_users() {
    echo -e "${BLUE}=== Currently Logged On Users ===${NC}"
    who
}

# Function hiển thị memory usage
display_memory_usage() {
    echo -e "${BLUE}=== Memory Usage Information ===${NC}"
    free -h
}

# Function xác nhận yes/no
confirm_action() {
    local prompt="$1"
    local response
    
    while true; do
        echo -n -e "${RED}$prompt (yes/no): ${NC}"
        read -r response
        case "$response" in
            [yY]|[yY][eE][sS]) return 0 ;;
            [nN]|[nN][oO]) return 1 ;;
            *) echo "Please answer yes or no." ;;
        esac
    done
}

# Function reboot system
reboot_system() {
    echo -e "${RED}WARNING: System will be rebooted!${NC}"
    if confirm_action "Are you sure you want to reboot?"; then
        echo "Rebooting system..."
        sudo reboot
    else
        echo "Reboot cancelled."
    fi
}

# Function poweroff system
poweroff_system() {
    echo -e "${RED}WARNING: System will be powered off!${NC}"
    if confirm_action "Are you sure you want to power off?"; then
        echo "Powering off system..."
        sudo poweroff
    else
        echo "Power off cancelled."
    fi
}

# Function thoát chương trình
exit_program() {
    if confirm_action "Are you sure you want to exit?"; then
        echo -e "${GREEN}Thank you for using System Admin Menu${NC}"
        sleep 2
        clear
        exit 0
    fi
}

# Function hiển thị menu
show_menu() {
    clear
    echo -e "${GREEN}=== System Admin Menu ===${NC}"
    echo "1. Display disk space"
    echo "2. Display logged on users"
    echo "3. Display memory usage"
    echo "4. Reboot system"
    echo "5. Poweroff system"
    echo "6. Exit program"
    echo -e "${BLUE}Enter option:${NC}"
}

# Function xử lý lựa chọn menu
handle_option() {
    local choice="$1"
    case $choice in
        1) display_disk_space ;;
        2) display_logged_users ;;
        3) display_memory_usage ;;
        4) reboot_system ;;
        5) poweroff_system ;;
        6) exit_program ;;
        *) echo -e "${RED}Invalid option!${NC}" ;;
    esac
}

# Main loop
while true; do
    show_menu
    read -r choice
    handle_option "$choice"
    
    # Pause after each action
    if [ "$choice" != "6" ]; then
        echo -e "\nPress Enter to continue..."
        read -r
    fi
done
```

## III. Giải thích chi tiết từng phần

### 1. Định nghĩa màu sắc
```bash
GREEN='\033[0;32m'
RED='\033[0;31m'
BLUE='\033[0;34m'
NC='\033[0m'
```
**Lý do:**
- Giúp giao diện dễ nhìn
- Làm nổi bật các thông báo quan trọng
- Phân biệt các phần khác nhau

### 2. Functions hiển thị thông tin
```bash
display_disk_space() {
    echo -e "${BLUE}=== Disk Space Information ===${NC}"
    df -h | grep '^/dev'
}
```
**Lý do:**
- Tách riêng từng chức năng
- Dễ maintain và debug
- Có thể tái sử dụng

### 3. Function xác nhận
```bash
confirm_action() {
    local prompt="$1"
    while true; do
        echo -n -e "${RED}$prompt (yes/no): ${NC}"
        read -r response
        case "$response" in
            [yY]|[yY][eE][sS]) return 0 ;;
            [nN]|[nN][oO]) return 1 ;;
            *) echo "Please answer yes or no." ;;
        esac
    done
}
```
**Lý do:**
- Tránh thực hiện nhầm các lệnh nguy hiểm
- Xử lý input linh hoạt (yes/no/y/n)
- Lặp lại nếu input không hợp lệ

### 4. Functions system control
```bash
reboot_system() {
    echo -e "${RED}WARNING: System will be rebooted!${NC}"
    if confirm_action "Are you sure you want to reboot?"; then
        echo "Rebooting system..."
        sudo reboot
    fi
}
```
**Lý do:**
- Cảnh báo người dùng trước khi thực hiện
- Yêu cầu xác nhận để tránh lỗi
- Sử dụng sudo cho các lệnh hệ thống

### 5. Menu handling
```bash
show_menu() {
    clear
    echo -e "${GREEN}=== System Admin Menu ===${NC}"
    ...
}

handle_option() {
    local choice="$1"
    case $choice in
        1) display_disk_space ;;
        ...
    esac
}
```
**Lý do:**
- Tách riêng phần hiển thị và xử lý
- Code rõ ràng, dễ đọc
- Dễ thêm/sửa options

## IV. Cách sử dụng

### 1. Setup
```bash
# Tạo file script
vi system_admin.sh

# Phân quyền thực thi
chmod +x system_admin.sh

# Chạy script
sudo ./system_admin.sh
```

### 2. Examples
```bash
# Xem disk space
1<Enter>

# Reboot với confirm
4<Enter>
yes

# Exit với goodbye
6<Enter>
yes
```

## V. Best Practices

### 1. Security
- Sử dụng sudo cho các lệnh hệ thống
- Confirm trước các thao tác nguy hiểm
- Kiểm tra quyền thực thi

### 2. User Interface
- Menu rõ ràng, dễ đọc
- Thông báo màu sắc hợp lý
- Phản hồi cho mỗi thao tác

### 3. Code Organization
- Functions riêng biệt
- Comment đầy đủ
- Error handling

## VI. Mở rộng

1. **Thêm tính năng:**
   - System monitoring
   - Service management
   - Network diagnostics

2. **Cải thiện interface:**
   - Dialog-based menu
   - Progress bars
   - Logging

3. **Security:**
   - User authentication
   - Action logging
   - Permission checking

---

*Note: Script này dành cho mục đích học tập. Trong môi trường production cần thêm nhiều xử lý bảo mật.*