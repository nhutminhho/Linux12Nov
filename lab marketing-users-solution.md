# Hướng Dẫn: Tạo 50 Users Marketing

## Bước 1: Tạo File Script

```bash
# Tạo file script
nano create_marketing_users.sh
```

## Bước 2: Nội Dung Script

```bash
#!/bin/bash

# 1. Kiểm tra quyền root
if [ "$(id -u)" -ne 0 ]; then
    echo "Script này phải chạy với quyền root"
    exit 1
fi

# 2. Tạo group marketing
if ! getent group marketing > /dev/null; then
    groupadd marketing
    echo "Đã tạo group marketing"
else
    echo "Group marketing đã tồn tại"
fi

# 3. Tạo 50 users
for i in {1..50}; do
    # Tạo tên user
    username="mar-user$i"
    
    # Kiểm tra user đã tồn tại chưa
    if id "$username" &>/dev/null; then
        echo "User $username đã tồn tại"
        continue
    fi
    
    # Tạo user
    useradd -m \
        -g marketing \
        -s /bin/bash \
        -c "Marketing User $i" \
        "$username"
    
    # Đặt mật khẩu
    echo "${username}:Marketing@2024" | chpasswd
    
    echo "Đã tạo user $username"
done

# 4. Hiển thị kết quả
echo "==== Kết quả ===="
echo "Số users đã tạo: $(grep mar-user /etc/passwd | wc -l)"
echo "Danh sách users:"
grep mar-user /etc/passwd | cut -d: -f1
```

## Giải Thích Chi Tiết

### 1. Kiểm Tra Quyền Root
```bash
if [ "$(id -u)" -ne 0 ]; then
    echo "Script này phải chạy với quyền root"
    exit 1
fi
```
**Giải thích:**
- Chỉ root mới có quyền tạo user/group
- `id -u` trả về ID của user hiện tại
- Root có ID = 0
- Nếu không phải root thì thoát script

### 2. Tạo Group Marketing
```bash
if ! getent group marketing > /dev/null; then
    groupadd marketing
fi
```
**Giải thích:**
- `getent group`: kiểm tra group có tồn tại không
- `> /dev/null`: ẩn output
- `groupadd`: tạo group mới
- Kiểm tra trước khi tạo để tránh lỗi

### 3. Vòng Lặp Tạo Users
```bash
for i in {1..50}; do
    username="mar-user$i"
```
**Giải thích:**
- Dùng for loop để tạo 50 users
- `{1..50}`: sequence từ 1 đến 50
- Tên user theo format: mar-user + số thứ tự

### 4. Tạo User với useradd
```bash
useradd -m \
    -g marketing \
    -s /bin/bash \
    -c "Marketing User $i" \
    "$username"
```
**Giải thích:**
- `-m`: tạo home directory
- `-g marketing`: đặt primary group
- `-s /bin/bash`: đặt default shell
- `-c`: thêm comment/description
- Các options được tách dòng để dễ đọc

### 5. Đặt Mật Khẩu
```bash
echo "${username}:Marketing@2024" | chpasswd
```
**Giải thích:**
- Dùng `chpasswd` để đặt mật khẩu
- Format: "username:password"
- Mật khẩu phức tạp: chữ, số, ký tự đặc biệt

## Các Bước Thực Hiện

1. **Tạo và Lưu Script:**
```bash
sudo nano create_marketing_users.sh
# Copy nội dung script vào
```

2. **Phân Quyền Execute:**
```bash
sudo chmod +x create_marketing_users.sh
```

3. **Chạy Script:**
```bash
sudo ./create_marketing_users.sh
```

4. **Kiểm Tra Kết Quả:**
```bash
# Xem danh sách users
grep mar-user /etc/passwd

# Xem thành viên group
getent group marketing

# Kiểm tra home directories
ls -l /home | grep mar-user
```

## Tips và Lưu Ý

### 1. Bảo Mật
- Đặt mật khẩu phức tạp
- Yêu cầu đổi mật khẩu lần đầu
- Giới hạn quyền truy cập

### 2. Quản Lý
- Ghi log mọi hoạt động
- Backup trước khi thực hiện
- Document đầy đủ

### 3. Maintenance
- Kiểm tra định kỳ
- Cập nhật mật khẩu
- Xóa users không dùng

## Xử Lý Lỗi

### 1. Lỗi Permissions
```bash
sudo chown -R username:marketing /home/username
sudo chmod 700 /home/username
```

### 2. Lỗi Tạo User
```bash
# Kiểm tra log
tail -f /var/log/auth.log

# Kiểm tra disk space
df -h
```

