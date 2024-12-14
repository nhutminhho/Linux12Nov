# Hướng dẫn thực hiện Script Quản lý User và Group

## I. Cấu trúc thư mục dự án

```plaintext
user_management/
├── main.sh                # Script menu chính
├── lib/
│   ├── utils.sh          # Các hàm tiện ích dùng chung
│   └── validation.sh     # Các hàm kiểm tra input
└── scripts/
    ├── create_user.sh
    ├── delete_user.sh
    ├── change_password.sh
    ├── view_user.sh
    ├── create_group.sh
    ├── add_to_group.sh
    ├── remove_from_group.sh
    ├── list_group_members.sh
    └── list_groups.sh
```

## II. Tạo các file cơ bản

### 1. Script menu chính (main.sh)
```bash
#!/bin/bash

# File: main.sh
# Mục đích: Script menu chính để quản lý user và group

# Định nghĩa màu sắc
GREEN='\033[0;32m'
BLUE='\033[0;34m'
RED='\033[0;31m'
NC='\033[0m'  # No Color

# Đường dẫn tới thư mục scripts
SCRIPTS_DIR="./scripts"

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
        1) $SCRIPTS_DIR/create_user.sh ;;
        2) $SCRIPTS_DIR/delete_user.sh ;;
        3) $SCRIPTS_DIR/change_password.sh ;;
        4) $SCRIPTS_DIR/view_user.sh ;;
        5) $SCRIPTS_DIR/create_group.sh ;;
        6) $SCRIPTS_DIR/add_to_group.sh ;;
        7) $SCRIPTS_DIR/remove_from_group.sh ;;
        8) $SCRIPTS_DIR/list_group_members.sh ;;
        9) $SCRIPTS_DIR/list_groups.sh ;;
        10) echo -e "${GREEN}Goodbye!${NC}"; exit 0 ;;
        *) echo -e "${RED}Invalid option!${NC}" ;;
    esac
}

# Main loop
while true; do
    show_menu
    read -r choice
    handle_option "$choice"
    echo -e "\nPress Enter to continue..."
    read -r
done
```

### 2. Utility Functions (lib/utils.sh)
```bash
#!/bin/bash

# File: lib/utils.sh
# Mục đích: Các hàm tiện ích dùng chung

# Kiểm tra user có tồn tại không
check_user_exists() {
    if id "$1" &>/dev/null; then
        return 0  # User tồn tại
    else
        return 1  # User không tồn tại
    fi
}

# Kiểm tra group có tồn tại không
check_group_exists() {
    if getent group "$1" &>/dev/null; then
        return 0  # Group tồn tại
    else
        return 1  # Group không tồn tại
    fi
}

# Hiển thị thông báo lỗi
show_error() {
    echo -e "${RED}Error: $1${NC}"
}

# Hiển thị thông báo thành công
show_success() {
    echo -e "${GREEN}Success: $1${NC}"
}
```

### 3. Delete User Script (scripts/delete_user.sh)
```bash
#!/bin/bash

# File: scripts/delete_user.sh
# Mục đích: Xóa user với các tùy chọn

# Import utils
source lib/utils.sh

# Nhập tên user cần xóa
echo -n "Enter username to delete: "
read -r username

# Kiểm tra user có tồn tại không
if ! check_user_exists "$username"; then
    show_error "User does not exist"
    exit 1
fi

# Hỏi có xóa home directory không
echo -n "Delete home directory? (YES/NO): "
read -r choice

case $choice in
    YES)
        if userdel -r "$username"; then
            show_success "User and home directory deleted successfully"
        else
            show_error "Failed to delete user"
        fi
        ;;
    NO)
        if userdel "$username"; then
            show_success "User deleted (home directory preserved)"
        else
            show_error "Failed to delete user"
        fi
        ;;
    *)
        show_error "Wrong Input"
        exit 1
        ;;
esac
```

### 4. Remove from Group Script (scripts/remove_from_group.sh)
```bash
#!/bin/bash

# File: scripts/remove_from_group.sh
# Mục đích: Xóa user khỏi group

# Import utils
source lib/utils.sh

# Nhập tên user
echo -n "Enter username: "
read -r username

# Kiểm tra user
if ! check_user_exists "$username"; then
    show_error "User does not exist"
    exit 1
fi

# Nhập tên group
echo -n "Enter group name: "
read -r groupname

# Kiểm tra group
if ! check_group_exists "$groupname"; then
    show_error "Group does not exist"
    exit 1
fi

# Xóa user khỏi group
if gpasswd -d "$username" "$groupname"; then
    show_success "User removed from group successfully"
else
    show_error "Failed to remove user from group"
fi
```

## III. Giải thích chi tiết

### 1. Cấu trúc modular
- Tách thành nhiều file riêng biệt để:
  - Dễ maintain và debug
  - Có thể tái sử dụng code
  - Phân chia trách nhiệm rõ ràng

### 2. Error Handling
- Kiểm tra input trước khi thực hiện
- Thông báo lỗi rõ ràng
- Exit với mã lỗi phù hợp

### 3. User Interface
- Sử dụng màu sắc cho dễ đọc
- Menu rõ ràng, dễ hiểu
- Thông báo kết quả chi tiết

## IV. Hướng dẫn sử dụng

### 1. Setup
```bash
# Tạo cấu trúc thư mục
mkdir -p user_management/{lib,scripts}

# Copy các file script vào đúng vị trí
# Phân quyền thực thi
chmod +x user_management/main.sh
chmod +x user_management/scripts/*
```

### 2. Chạy chương trình
```bash
# Di chuyển vào thư mục dự án
cd user_management

# Chạy script chính
sudo ./main.sh
```

## V. Test Cases

### 1. Delete User
```bash
# Test user không tồn tại
Enter username to delete: nonexistent_user
> Error: User does not exist

# Test xóa user với home directory
Enter username to delete: testuser
Delete home directory? (YES/NO): YES
> Success: User and home directory deleted successfully
```

### 2. Remove from Group
```bash
# Test user không tồn tại
Enter username: nonexistent_user
> Error: User does not exist

# Test group không tồn tại
Enter username: existinguser
Enter group name: nonexistent_group
> Error: Group does not exist
```

## VI. Best Practices

### 1. Security
- Sử dụng sudo khi cần
- Validate input cẩn thận
- Kiểm tra quyền truy cập

### 2. Code Quality
- Comment đầy đủ
- Indent code rõ ràng
- Tên biến và hàm có ý nghĩa

### 3. Error Handling
- Kiểm tra kết quả mỗi lệnh
- Thông báo lỗi chi tiết
- Log lỗi khi cần

## VII. Mở rộng

1. **Thêm tính năng:**
   - Backup user data
   - Batch processing
   - User quota management

2. **Cải thiện interface:**
   - Thêm GUI với dialog
   - Progress bars
   - Interactive menus

3. **Logging:**
   - Log file operations
   - Audit trail
   - Error logging

---

*Note: Script này dành cho mục đích học tập. Trong môi trường production cần thêm nhiều xử lý bảo mật.*