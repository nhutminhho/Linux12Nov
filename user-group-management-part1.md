# Hướng dẫn chi tiết Quản lý User và Group

## I. Cấu trúc project

```plaintext
user_management/
├── scripts/
│   ├── create_user.sh
│   ├── delete_user.sh
│   ├── change_password.sh
│   ├── view_user.sh
│   ├── create_group.sh
│   ├── add_to_group.sh
│   ├── remove_from_group.sh
│   ├── list_group_members.sh
│   └── list_groups.sh
├── lib/
│   ├── colors.sh
│   ├── utils.sh
│   └── validation.sh
├── main.sh
└── README.md
```

## II. Các file cơ bản

### 1. Colors và Utilities (lib/colors.sh)
```bash
#!/bin/bash
# File: lib/colors.sh
# Mục đích: Định nghĩa màu sắc và hàm hiển thị

# Định nghĩa màu
export GREEN='\033[0;32m'
export RED='\033[0;31m'
export BLUE='\033[0;34m'
export NC='\033[0m'  # No Color

# Hàm hiển thị thông báo lỗi
show_error() {
    echo -e "${RED}Error: $1${NC}"
}

# Hàm hiển thị thông báo thành công
show_success() {
    echo -e "${GREEN}Success: $1${NC}"
}
```

### 2. Validation Functions (lib/validation.sh)
```bash
#!/bin/bash
# File: lib/validation.sh
# Mục đích: Kiểm tra tính hợp lệ của input

# Load colors
source "$(dirname "$0")/colors.sh"

# Kiểm tra user có tồn tại không
check_user_exists() {
    local username=$1
    if id "$username" &>/dev/null; then
        return 0  # User tồn tại
    else
        return 1  # User không tồn tại
    fi
}

# Kiểm tra group có tồn tại không
check_group_exists() {
    local groupname=$1
    if getent group "$groupname" &>/dev/null; then
        return 0  # Group tồn tại
    else
        return 1  # Group không tồn tại
    fi
}

# Kiểm tra input yes/no
confirm_action() {
    local prompt=$1
    local response
    
    while true; do
        read -p "$prompt (YES/NO): " response
        case "$response" in
            [Yy][Ee][Ss]) return 0 ;;
            [Nn][Oo]) return 1 ;;
            *) echo "Wrong Input" ;;
        esac
    done
}
```

### 3. Create User Script (scripts/create_user.sh)
```bash
#!/bin/bash
# File: scripts/create_user.sh
# Mục đích: Tạo user mới

# Load các thư viện
source "$(dirname "$0")/../lib/colors.sh"
source "$(dirname "$0")/../lib/validation.sh"

# Hàm tạo user
create_user() {
    echo -e "${BLUE}=== Create New User ===${NC}"
    
    # Nhập username
    read -p "Enter username: " username
    
    # Kiểm tra user đã tồn tại chưa
    if check_user_exists "$username"; then
        show_error "User already exists"
        return 1
    fi
    
    # Tạo user
    if useradd -m "$username"; then
        # Set password
        passwd "$username"
        show_success "User $username created successfully"
    else
        show_error "Failed to create user"
        return 1
    fi
}

# Chạy function nếu script được gọi trực tiếp
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    create_user
fi
```

### 4. Delete User Script (scripts/delete_user.sh)
```bash
#!/bin/bash
# File: scripts/delete_user.sh
# Mục đích: Xóa user

# Load các thư viện
source "$(dirname "$0")/../lib/colors.sh"
source "$(dirname "$0")/../lib/validation.sh"

# Hàm xóa user
delete_user() {
    echo -e "${BLUE}=== Delete User ===${NC}"
    
    # Nhập username
    read -p "Enter username to delete: " username
    
    # Kiểm tra user có tồn tại không
    if ! check_user_exists "$username"; then
        show_error "User does not exist"
        return 1
    fi
    
    # Hỏi về việc xóa home directory
    if confirm_action "Delete home directory?"; then
        if userdel -r "$username"; then
            show_success "User and home directory deleted"
        else
            show_error "Failed to delete user"
            return 1
        fi
    else
        if userdel "$username"; then
            show_success "User deleted (home directory preserved)"
        else
            show_error "Failed to delete user"
            return 1
        fi
    fi
}

# Chạy function nếu script được gọi trực tiếp
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    delete_user
fi
```

### 5. Main Menu Script (main.sh)
```bash
#!/bin/bash
# File: main.sh
# Mục đích: Menu chính của chương trình

# Load thư viện
source "$(dirname "$0")/lib/colors.sh"

# Định nghĩa đường dẫn scripts
SCRIPTS_DIR="$(dirname "$0")/scripts"

# Hiển thị menu
show_menu() {
    clear
    echo -e "${GREEN}=== User and Group Administration ===${NC}"
    echo "1. Create user"
    echo "2. Delete user"
    echo "3. Change user password"
    echo "4. View user details"
    echo "5. Create group"
    echo "6. Add user to group"
    echo "7. Remove user from group"
    echo "8. List group members"
    echo "9. List existing groups"
    echo "10. Exit"
    echo -e "${BLUE}Enter option:${NC}"
}

# Xử lý lựa chọn menu
handle_option() {
    case $1 in
        1) bash "$SCRIPTS_DIR/create_user.sh" ;;
        2) bash "$SCRIPTS_DIR/delete_user.sh" ;;
        3) bash "$SCRIPTS_DIR/change_password.sh" ;;
        4) bash "$SCRIPTS_DIR/view_user.sh" ;;
        5) bash "$SCRIPTS_DIR/create_group.sh" ;;
        6) bash "$SCRIPTS_DIR/add_to_group.sh" ;;
        7) bash "$SCRIPTS_DIR/remove_from_group.sh" ;;
        8) bash "$SCRIPTS_DIR/list_group_members.sh" ;;
        9) bash "$SCRIPTS_DIR/list_groups.sh" ;;
        10) echo -e "${GREEN}Goodbye!${NC}"; exit 0 ;;
        *) echo -e "${RED}Invalid option!${NC}" ;;
    esac
}

# Main loop
while true; do
    show_menu
    read -p "Enter choice: " choice
    handle_option "$choice"
    
    # Pause after each action
    if [ "$choice" != "10" ]; then
        echo -e "\nPress Enter to continue..."
        read
    fi
done
```

## III. Giải thích chi tiết các lệnh

### 1. Các lệnh quản lý user:

1. **useradd**: Tạo user mới
```bash
useradd -m username
# -m: Tạo home directory
```

2. **userdel**: Xóa user
```bash
userdel -r username
# -r: Xóa cả home directory
```

3. **passwd**: Đổi password
```bash
passwd username
```

### 2. Các lệnh quản lý group:

1. **groupadd**: Tạo group mới
```bash
groupadd groupname
```

2. **groupdel**: Xóa group
```bash
groupdel groupname
```

3. **usermod**: Thêm user vào group
```bash
usermod -aG groupname username
# -a: append
# -G: supplementary groups
```

### 3. Các lệnh kiểm tra:

1. **id**: Kiểm tra thông tin user
```bash
id username
```

2. **getent**: Kiểm tra thông tin group
```bash
getent group groupname
```

## IV. Thực hành và test

### 1. Setup môi trường
```bash
# Tạo cấu trúc thư mục
mkdir -p user_management/{scripts,lib}

# Copy các file vào đúng vị trí

# Phân quyền thực thi
chmod +x user_management/main.sh
chmod +x user_management/scripts/*
```

### 2. Test cases
```bash
# Test tạo user
./scripts/create_user.sh
# Nhập username: testuser

# Test xóa user
./scripts/delete_user.sh
# Nhập username: testuser
# Chọn YES để xóa home directory
```

## V. Lưu ý quan trọng

### 1. Bảo mật:
- Luôn chạy với sudo khi cần
- Validate input cẩn thận
- Không lưu password trong script

### 2. Error handling:
- Kiểm tra kết quả mỗi lệnh
- Thông báo lỗi rõ ràng
- Exit với mã lỗi phù hợp

### 3. Best practices:
- Comment đầy đủ
- Tổ chức code rõ ràng
- Log các thao tác quan trọng

---

*Note: Script này dành cho mục đích học tập. Trong môi trường production cần thêm nhiều biện pháp bảo mật.*