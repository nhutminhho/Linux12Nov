# Hướng dẫn tổ chức Project System Admin

## I. Cấu trúc thư mục

```plaintext
system_admin/
├── src/
│   ├── functions/
│   │   ├── display.sh     # Các hàm hiển thị
│   │   ├── system.sh      # Các hàm hệ thống
│   │   └── menu.sh        # Các hàm menu
│   ├── utils/
│   │   ├── colors.sh      # Định nghĩa màu sắc
│   │   └── confirm.sh     # Hàm xác nhận
│   └── main.sh            # File chính
└── README.md              # Hướng dẫn sử dụng
```

## II. Chi tiết từng file

### 1. colors.sh - Định nghĩa màu sắc
```bash
#!/bin/bash
# File: src/utils/colors.sh
# Mục đích: Định nghĩa các màu sắc dùng chung

# Màu sắc cho menu
export GREEN='\033[0;32m'
export RED='\033[0;31m'
export BLUE='\033[0;34m'
export NC='\033[0m'  # No Color
```

### 2. confirm.sh - Hàm xác nhận
```bash
#!/bin/bash
# File: src/utils/confirm.sh
# Mục đích: Xác nhận các hành động quan trọng

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
```

### 3. display.sh - Các hàm hiển thị
```bash
#!/bin/bash
# File: src/functions/display.sh
# Mục đích: Các hàm hiển thị thông tin hệ thống

# Load màu sắc
source "$(dirname "$0")/../utils/colors.sh"

# Hiển thị disk space
display_disk_space() {
    echo -e "${BLUE}=== Disk Space Information ===${NC}"
    df -h | grep '^/dev'
}

# Hiển thị users đang logged on
display_logged_users() {
    echo -e "${BLUE}=== Currently Logged On Users ===${NC}"
    who
}

# Hiển thị memory usage
display_memory_usage() {
    echo -e "${BLUE}=== Memory Usage Information ===${NC}"
    free -h
}
```

### 4. system.sh - Các hàm hệ thống
```bash
#!/bin/bash
# File: src/functions/system.sh
# Mục đích: Các hàm thao tác với hệ thống

# Load các utils
source "$(dirname "$0")/../utils/colors.sh"
source "$(dirname "$0")/../utils/confirm.sh"

# Reboot hệ thống
reboot_system() {
    echo -e "${RED}WARNING: System will be rebooted!${NC}"
    if confirm_action "Are you sure you want to reboot?"; then
        echo "Rebooting system..."
        sudo reboot
    else
        echo "Reboot cancelled."
    fi
}

# Poweroff hệ thống
poweroff_system() {
    echo -e "${RED}WARNING: System will be powered off!${NC}"
    if confirm_action "Are you sure you want to power off?"; then
        echo "Powering off system..."
        sudo poweroff
    else
        echo "Power off cancelled."
    fi
}
```

### 5. menu.sh - Các hàm menu
```bash
#!/bin/bash
# File: src/functions/menu.sh
# Mục đích: Xử lý menu và lựa chọn

# Load màu sắc
source "$(dirname "$0")/../utils/colors.sh"

# Hiển thị menu
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
```

### 6. main.sh - File chính
```bash
#!/bin/bash
# File: src/main.sh
# Mục đích: Script chính điều khiển chương trình

# Load các modules
SCRIPT_DIR="$(dirname "$0")"
source "$SCRIPT_DIR/utils/colors.sh"
source "$SCRIPT_DIR/utils/confirm.sh"
source "$SCRIPT_DIR/functions/display.sh"
source "$SCRIPT_DIR/functions/system.sh"
source "$SCRIPT_DIR/functions/menu.sh"

# Hàm thoát chương trình
exit_program() {
    if confirm_action "Are you sure you want to exit?"; then
        echo -e "${GREEN}Thank you for using System Admin Menu${NC}"
        sleep 2
        clear
        exit 0
    fi
}

# Xử lý lựa chọn menu
handle_option() {
    case $1 in
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
    
    if [ "$choice" != "6" ]; then
        echo -e "\nPress Enter to continue..."
        read -r
    fi
done
```

## III. Giải thích chi tiết

### 1. Tại sao cần tổ chức theo cấu trúc thư mục?

1. **Tách biệt chức năng:**
   - Mỗi file đảm nhận một nhiệm vụ riêng
   - Dễ tìm và sửa code
   - Tránh file quá dài và phức tạp

2. **Dễ bảo trì:**
   - Sửa một chức năng không ảnh hưởng các phần khác
   - Thêm tính năng mới dễ dàng
   - Code rõ ràng, dễ đọc

3. **Tái sử dụng code:**
   - Các utils dùng chung cho nhiều nơi
   - Không cần viết lại code
   - Dễ chia sẻ code với người khác

### 2. Cách file kết nối với nhau

1. **Source command:**
```bash
source "$(dirname "$0")/../utils/colors.sh"
```
- `$(dirname "$0")`: Lấy thư mục hiện tại
- `../`: Di chuyển lên thư mục cha
- Load các biến và hàm từ file khác

2. **Export variables:**
```bash
export GREEN='\033[0;32m'
```
- Biến có thể dùng ở các file khác
- Không cần khai báo lại

### 3. Cách chạy chương trình

1. **Setup:**
```bash
# Tạo cấu trúc thư mục
mkdir -p system_admin/src/{functions,utils}

# Copy các file vào đúng vị trí
# Phân quyền thực thi
chmod +x system_admin/src/main.sh
```

2. **Chạy chương trình:**
```bash
cd system_admin/src
./main.sh
```

## IV. Best Practices

1. **Comment đầy đủ:**
   - Mô tả mục đích của file/function
   - Giải thích code phức tạp
   - Hướng dẫn sử dụng

2. **Đặt tên rõ ràng:**
   - Tên file mô tả chức năng
   - Tên function dễ hiểu
   - Tên biến có ý nghĩa

3. **Xử lý lỗi:**
   - Kiểm tra input
   - Thông báo lỗi rõ ràng
   - Có plan backup

## V. Tips cho người mới

1. **Bắt đầu đơn giản:**
   - Hiểu rõ từng file một
   - Test mỗi function riêng
   - Thêm tính năng dần dần

2. **Debug hiệu quả:**
   - Thêm echo để xem giá trị biến
   - Test từng phần nhỏ
   - Đọc error messages cẩn thận

3. **Tổ chức code:**
   - Giữ mỗi file/function ngắn gọn
   - Comment đầy đủ và rõ ràng
   - Đặt tên có ý nghĩa

---

*Note: Hướng dẫn này dành cho người mới học. Trong thực tế có thể cần thêm nhiều xử lý phức tạp hơn.*