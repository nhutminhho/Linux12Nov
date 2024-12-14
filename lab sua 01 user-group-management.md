# Lab Thực Hành: Script Quản Lý User và Group

## 1. Cấu Trúc Thư Mục

```bash
/scripts/
├── menu.sh            # Script chính
├── create_user.sh     # Tạo user
├── delete_user.sh     # Xóa user
├── change_passwd.sh   # Đổi mật khẩu
├── view_user.sh       # Xem thông tin user
├── create_group.sh    # Tạo group
├── add_to_group.sh    # Thêm user vào group
├── remove_from_group.sh # Xóa user khỏi group
├── list_members.sh    # Liệt kê thành viên group
└── list_groups.sh     # Liệt kê groups
```

## 2. Script Chính (menu.sh)

```bash
#!/bin/bash

# Định nghĩa đường dẫn
SCRIPT_DIR="$(dirname "$0")"

while true; do
    clear
    echo "User and Group Administration"
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
    
    read -p "Enter option: " option
    
    case $option in
        1) bash "$SCRIPT_DIR/create_user.sh" ;;
        2) bash "$SCRIPT_DIR/delete_user.sh" ;;
        3) bash "$SCRIPT_DIR/change_passwd.sh" ;;
        4) bash "$SCRIPT_DIR/view_user.sh" ;;
        5) bash "$SCRIPT_DIR/create_group.sh" ;;
        6) bash "$SCRIPT_DIR/add_to_group.sh" ;;
        7) bash "$SCRIPT_DIR/remove_from_group.sh" ;;
        8) bash "$SCRIPT_DIR/list_members.sh" ;;
        9) bash "$SCRIPT_DIR/list_groups.sh" ;;
        10) echo "Goodbye!"; exit 0 ;;
        *) echo "Invalid option!"; sleep 2 ;;
    esac
done
```

## 3. Script Con

### create_user.sh
```bash
#!/bin/bash

read -p "Enter username: " username

# Kiểm tra user đã tồn tại
if id "$username" &>/dev/null; then
    echo "User already exists!"
    exit 1
fi

# Tạo user với home directory
if useradd -m "$username"; then
    # Đặt mật khẩu
    read -s -p "Enter password: " password
    echo
    echo "$password" | passwd --stdin "$username"
    echo "User $username created successfully"
else
    echo "Failed to create user!"
fi
```

### delete_user.sh
```bash
#!/bin/bash

read -p "Enter username to delete: " username

# Kiểm tra user tồn tại
if ! id "$username" &>/dev/null; then
    echo "User does not exist"
    exit 1
fi

# Hỏi về xóa home directory
read -p "Delete home directory? (YES/NO): " answer
case "$answer" in
    YES)
        userdel -r "$username"
        echo "User and home directory deleted"
        ;;
    NO)
        userdel "$username"
        echo "User deleted, home directory preserved"
        ;;
    *)
        echo "Wrong Input"
        ;;
esac
```

### remove_from_group.sh
```bash
#!/bin/bash

read -p "Enter username: " username
read -p "Enter group name: " groupname

# Kiểm tra user
if ! id "$username" &>/dev/null; then
    echo "User does not exist"
    exit 1
fi

# Kiểm tra group
if ! getent group "$groupname" &>/dev/null; then
    echo "Group does not exist"
    exit 1
fi

# Xóa user khỏi group
if gpasswd -d "$username" "$groupname"; then
    echo "User removed from group successfully"
else
    echo "Failed to remove user from group"
fi
```

## 4. Xử Lý Lỗi và Validation

### Kiểm Tra Quyền Root
```bash
# Thêm vào đầu mỗi script
if [ "$(id -u)" -ne 0 ]; then
    echo "This script must be run as root"
    exit 1
fi
```

### Validate Input
```bash
# Kiểm tra username hợp lệ
validate_username() {
    local username="$1"
    if [[ ! "$username" =~ ^[a-z][-a-z0-9]*$ ]]; then
        echo "Invalid username format"
        return 1
    fi
    return 0
}
```

### Xử Lý Tín Hiệu
```bash
# Thêm vào script chính
trap 'echo "Script interrupted"; exit 1' INT TERM
```

## 5. Functions Hữu Ích

### Hiển Thị User Info
```bash
show_user_info() {
    local username="$1"
    echo "User Information:"
    id "$username"
    echo "Groups:"
    groups "$username"
    echo "Home Directory:"
    ls -ld "/home/$username"
}
```

### Kiểm Tra Group Membership
```bash
check_group_membership() {
    local username="$1"
    local groupname="$2"
    id -nG "$username" | grep -q "\b$groupname\b"
    return $?
}
```

## 6. Bài Tập Mở Rộng

### 1. Thêm Logging
```bash
# Tạo function log
log_action() {
    local message="$1"
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $message" >> /var/log/user_management.log
}
```

### 2. Backup trước khi xóa
```bash
backup_home() {
    local username="$1"
    local backup_dir="/backup/users"
    tar czf "$backup_dir/${username}_$(date +%Y%m%d).tar.gz" "/home/$username"
}
```

## 7. Tips và Best Practices

### 1. Bảo Mật
- Luôn kiểm tra input
- Sử dụng quotes cho biến
- Đặt umask phù hợp

### 2. Maintainability
- Comment code rõ ràng
- Tổ chức code theo modules
- Sử dụng tên biến có ý nghĩa

### 3. Testing
```bash
# Test script với:
- User/group không tồn tại
- Tên không hợp lệ
- Quyền không đủ
- Disk space không đủ
```

### 4. Documentation
```bash
# Thêm header cho mỗi script
: '
Script: create_user.sh
Purpose: Create new user with home directory
Usage: ./create_user.sh
Required privileges: root
'
```

