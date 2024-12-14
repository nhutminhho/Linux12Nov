# Lab Thực Hành: Kiểm Tra và Quản Lý Quyền File

## Phần 1: Hiểu Về File Permissions

### 1.1. Quyền Cơ Bản
```bash
# Các quyền trong Linux:
r (read)    - Quyền đọc
w (write)   - Quyền ghi
x (execute) - Quyền thực thi

# Kiểm tra quyền:
ls -l file.txt
# Ví dụ: -rw-r--r--
```

### 1.2. Chuẩn Bị File Test
```bash
# Tạo file test
echo "This is a test file" > test.txt

# Xem quyền mặc định
ls -l test.txt

# Giải thích:
# - File mới tạo thường có quyền rw-r--r--
# - Owner có quyền đọc/ghi
# - Group và Others chỉ có quyền đọc
```

## Phần 2: Script Kiểm Tra Quyền

### Bài 1: Script Cơ Bản
```bash
#!/bin/bash

# Nhận tên file từ người dùng
echo "Input name of file you wanna check: "
read FILE

# Kiểm tra quyền đọc và ghi
if [ -w "$FILE" ] && [ -r "$FILE" ]; then
    echo "Your file is okay with both of permission"
    ls -la "$FILE"
else
    ls -la "$FILE"
    echo "Your file is not with full read write permission"
    echo "==============================================="
    echo "Let me add read write permission to your file"
    chmod +rw "$FILE"
    ls -la "$FILE"
fi

# Giải thích từng phần:
# 1. read FILE: nhận input từ user
# 2. -w: kiểm tra quyền ghi
# 3. -r: kiểm tra quyền đọc
# 4. &&: kết hợp hai điều kiện
# 5. chmod +rw: thêm quyền đọc và ghi
```

## Phần 3: Thực Hành Chi Tiết

### Bài 1: Tạo và Test File
```bash
# 1. Tạo file test
echo "Content" > testfile.txt

# 2. Xóa quyền ghi
chmod -w testfile.txt

# 3. Chạy script để kiểm tra và sửa quyền
./check_permission.sh
# Nhập: testfile.txt

# Giải thích:
# - Tạo file để test
# - Xóa quyền để test script
# - Kiểm tra cách script hoạt động
```

### Bài 2: Script Nâng Cao
```bash
#!/bin/bash

# Nhận input và kiểm tra file tồn tại
echo "Input name of file you wanna check: "
read FILE

# Kiểm tra file có tồn tại không
if [ ! -f "$FILE" ]; then
    echo "Error: File does not exist!"
    exit 1
fi

# Hiển thị quyền hiện tại
echo "Current permissions:"
ls -l "$FILE"

# Kiểm tra từng quyền
echo -n "Read permission: "
[ -r "$FILE" ] && echo "YES" || echo "NO"

echo -n "Write permission: "
[ -w "$FILE" ] && echo "YES" || echo "NO"

# Thêm quyền nếu cần
if [ ! -r "$FILE" ] || [ ! -w "$FILE" ]; then
    echo "Adding missing permissions..."
    chmod +rw "$FILE"
    echo "New permissions:"
    ls -l "$FILE"
fi
```

## Phần 4: Bài Tập Thực Hành

### Bài 1: Kiểm Tra Nhiều File
```bash
#!/bin/bash

# Nhận danh sách file
echo "Enter files to check (space-separated):"
read -a FILES

# Kiểm tra từng file
for FILE in "${FILES[@]}"; do
    echo "Checking $FILE..."
    
    # Kiểm tra tồn tại
    if [ ! -f "$FILE" ]; then
        echo "$FILE does not exist"
        continue
    fi
    
    # Kiểm tra và sửa quyền
    if [ -w "$FILE" ] && [ -r "$FILE" ]; then
        echo "$FILE has correct permissions"
    else
        echo "Fixing permissions for $FILE"
        chmod +rw "$FILE"
    fi
    
    ls -l "$FILE"
    echo "----------------"
done
```

### Bài 2: Menu Tương Tác
```bash
#!/bin/bash

while true; do
    echo "File Permission Menu"
    echo "1. Check file permissions"
    echo "2. Add read permission"
    echo "3. Add write permission"
    echo "4. Remove read permission"
    echo "5. Remove write permission"
    echo "6. Exit"
    
    read -p "Choose option: " option
    
    case $option in
        1)
            read -p "Enter filename: " FILE
            ls -l "$FILE"
            ;;
        2)
            read -p "Enter filename: " FILE
            chmod +r "$FILE"
            ;;
        3)
            read -p "Enter filename: " FILE
            chmod +w "$FILE"
            ;;
        4)
            read -p "Enter filename: " FILE
            chmod -r "$FILE"
            ;;
        5)
            read -p "Enter filename: " FILE
            chmod -w "$FILE"
            ;;
        6)
            exit 0
            ;;
        *)
            echo "Invalid option"
            ;;
    esac
done
```

## Tips và Lưu Ý

### 1. Kiểm Tra An Toàn
```bash
# Luôn kiểm tra file tồn tại
[ ! -f "$FILE" ] && echo "File not found" && exit 1

# Kiểm tra quyền truy cập
[ ! -r "$FILE" ] && echo "Cannot read file" && exit 1
```

### 2. Best Practices
1. **Backup Trước Khi Thay Đổi:**
   ```bash
   cp "$FILE" "${FILE}.backup"
   ```

2. **Log Thay Đổi:**
   ```bash
   echo "$(date): Changed permissions for $FILE" >> changes.log
   ```

3. **Error Handling:**
   ```bash
   if ! chmod +rw "$FILE"; then
       echo "Error changing permissions"
       exit 1
   fi
   ```

### 3. Testing
```bash
# Test với các loại file khác nhau
touch test1.txt  # Regular file
ln -s test1.txt test2.txt  # Symbolic link
mkdir test_dir  # Directory
```

