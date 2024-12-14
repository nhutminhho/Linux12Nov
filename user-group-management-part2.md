# Phần tiếp theo của bài Quản lý User và Group

## I. Các scripts còn lại

### 1. Change Password (scripts/change_password.sh)
```bash
#!/bin/bash
# File: scripts/change_password.sh
# Mục đích: Thay đổi password cho user

# Load thư viện
source "$(dirname "$0")/../lib/colors.sh"
source "$(dirname "$0")/../lib/validation.sh"

# Hàm đổi password
change_password() {
    echo -e "${BLUE}=== Change User Password ===${NC}"
    
    # Nhập username
    read -p "Enter username: " username
    
    # Kiểm tra user tồn tại
    if ! check_user_exists "$username"; then
        show_error "User does not exist"
        return 1
    fi
    
    # Đổi password
    if passwd "$username"; then
        show_success "Password changed successfully"
    else
        show_error "Failed to change password"
        return 1
    fi
}

# Chạy function nếu script được gọi trực tiếp
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    change_password
fi
```

### 2. View User Details (scripts/view_user.sh)
```bash
#!/bin/bash
# File: scripts/view_user.sh
# Mục đích: Xem thông tin chi tiết của user

# Load thư viện
source "$(dirname "$0")/../lib/colors.sh"
source "$(dirname "$0")/../lib/validation.sh"

# Hàm xem thông tin user
view_user() {
    echo -e "${BLUE}=== User Details ===${NC}"
    
    # Nhập username
    read -p "Enter username: " username
    
    # Kiểm tra user tồn tại
    if ! check_user_exists "$username"; then
        show_error "User does not exist"
        return 1
    fi
    
    # Hiển thị thông tin chi tiết
    echo -e "\n${GREEN}User Information for $username:${NC}"
    echo "------------------------"
    id "$username"
    echo "------------------------"
    echo "Groups:"
    groups "$username"
    echo "------------------------"
    echo "Home directory:"
    eval echo ~"$username"
    echo "------------------------"
    echo "Shell:"
    getent passwd "$username" | cut -d: -f7
}

# Chạy function nếu script được gọi trực tiếp
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    view_user
fi
```

### 3. Create Group (scripts/create_group.sh)
```bash
#!/bin/bash
# File: scripts/create_group.sh
# Mục đích: Tạo group mới

# Load thư viện
source "$(dirname "$0")/../lib/colors.sh"
source "$(dirname "$0")/../lib/validation.sh"

# Hàm tạo group
create_group() {
    echo -e "${BLUE}=== Create New Group ===${NC}"
    
    # Nhập tên group
    read -p "Enter group name: " groupname
    
    # Kiểm tra group đã tồn tại chưa
    if check_group_exists "$groupname"; then
        show_error "Group already exists"
        return 1
    fi
    
    # Tạo group mới
    if groupadd "$groupname"; then
        show_success "Group $groupname created successfully"
    else
        show_error "Failed to create group"
        return 1
    fi
}

# Chạy function nếu script được gọi trực tiếp
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    create_group
fi
```

### 4. Add User to Group (scripts/add_to_group.sh)
```bash
#!/bin/bash
# File: scripts/add_to_group.sh
# Mục đích: Thêm user vào group

# Load thư viện
source "$(dirname "$0")/../lib/colors.sh"
source "$(dirname "$0")/../lib/validation.sh"

# Hàm thêm user vào group
add_to_group() {
    echo -e "${BLUE}=== Add User to Group ===${NC}"
    
    # Nhập thông tin
    read -p "Enter username: " username
    read -p "Enter group name: " groupname
    
    # Kiểm tra user và group
    if ! check_user_exists "$username"; then
        show_error "User does not exist"
        return 1
    fi
    
    if ! check_group_exists "$groupname"; then
        show_error "Group does not exist"
        return 1
    fi
    
    # Thêm user vào group
    if usermod -aG "$groupname" "$username"; then
        show_success "Added $username to group $groupname"
    else
        show_error "Failed to add user to group"
        return 1
    fi
}

# Chạy function nếu script được gọi trực tiếp
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    add_to_group
fi
```

### 5. List Group Members (scripts/list_group_members.sh)
```bash
#!/bin/bash
# File: scripts/list_group_members.sh
# Mục đích: Liệt kê thành viên của group

# Load thư viện
source "$(dirname "$0")/../lib/colors.sh"
source "$(dirname "$0")/../lib/validation.sh"

# Hàm liệt kê thành viên group
list_group_members() {
    echo -e "${BLUE}=== List Group Members ===${NC}"
    
    # Nhập tên group
    read -p "Enter group name: " groupname
    
    # Kiểm tra group tồn tại
    if ! check_group_exists "$groupname"; then
        show_error "Group does not exist"
        return 1
    }
    
    # Hiển thị thành viên
    echo -e "\n${GREEN}Members of group $groupname:${NC}"
    echo "------------------------"
    getent group "$groupname" | cut -d: -f4 | tr ',' '\n' | sort
}

# Chạy function nếu script được gọi trực tiếp
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    list_group_members
fi
```

### 6. List Existing Groups (scripts/list_groups.sh)
```bash
#!/bin/bash
# File: scripts/list_groups.sh
# Mục đích: Liệt kê tất cả groups trong hệ thống

# Load thư viện
source "$(dirname "$0")/../lib/colors.sh"

# Hàm liệt kê groups
list_groups() {
    echo -e "${BLUE}=== List All Groups ===${NC}"
    
    echo -e "\n${GREEN}System Groups:${NC}"
    echo "------------------------"
    getent group | sort | while IFS=: read -r name password gid members; do
        printf "%-20s (GID: %-5s) Members: %s\n" "$name" "$gid" "$members"
    done
}

# Chạy function nếu script được gọi trực tiếp
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    list_groups
fi
```

## II. Giải thích các lệnh quan trọng

### 1. Quản lý Users
1. `getent passwd username`: Lấy thông tin chi tiết về user
2. `groups username`: Xem các groups của user
3. `eval echo ~username`: Lấy đường dẫn home directory
4. `cut -d: -f7`: Lấy shell mặc định của user

### 2. Quản lý Groups
1. `getent group`: Liệt kê tất cả groups
2. `usermod -aG`: Thêm user vào group (-a: append, -G: supplementary groups)
3. `cut -d: -f4`: Lấy danh sách thành viên từ group entry

### 3. Xử lý text
1. `tr ',' '\n'`: Chuyển danh sách phân cách bằng dấu phẩy thành nhiều dòng
2. `sort`: Sắp xếp kết quả theo alphabet

## III. Cách sử dụng

### 1. Setup môi trường
```bash
# Tạo thư mục project
mkdir -p user_management/{scripts,lib}

# Copy files
cp *.sh user_management/
cp scripts/*.sh user_management/scripts/
cp lib/*.sh user_management/lib/

# Phân quyền
chmod +x user_management/main.sh
chmod +x user_management/scripts/*.sh
```

### 2. Chạy chương trình
```bash
cd user_management
sudo ./main.sh
```

## IV. Best Practices và Tips

### 1. Error Handling
```bash
# Luôn kiểm tra kết quả command
if command; then
    show_success "Success"
else
    show_error "Failed"
    return 1
fi
```

### 2. Input Validation
```bash
# Kiểm tra input rỗng
if [ -z "$input" ]; then
    show_error "Input cannot be empty"
    return 1
fi
```

### 3. Security
```bash
# Sanitize input
username=$(echo "$username" | tr -cd '[:alnum:]_-')
```

---

*Note: Scripts này dành cho mục đích học tập. Trong môi trường production cần thêm nhiều biện pháp bảo mật.*