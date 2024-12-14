# Bài Thực Hành: Tạo và Quản Lý Tài Khoản Người Dùng

## Chuẩn Bị
- Hệ thống Linux (AlmaLinux/CentOS)
- Quyền root hoặc sudo
- Terminal để thực hành

## Lab 1: Tạo User với Giá Trị Mặc Định

### Tình huống
Tạo một user mới có tên là student1 với các giá trị mặc định.

### Các bước thực hiện
```bash
# 1. Chuyển sang user root
sudo -i

# 2. Tạo user mới
useradd student1

# 3. Đặt mật khẩu
passwd student1
# Nhập mật khẩu: Student123@

# 4. Kiểm tra thông tin
grep student1 /etc/passwd
ls -l /home/student1
id student1
```

### Kết quả mong đợi
```
# Định dạng trong /etc/passwd sẽ như sau:
student1:x:1001:1001::/home/student1:/bin/bash
```

## Lab 2: Tạo User với Giá Trị Tùy Chỉnh

### Tình huống
Tạo user student2 với UID cụ thể, thư mục home tùy chỉnh và shell được chỉ định.

### Các bước thực hiện
```bash
# 1. Tạo user với các tùy chọn
useradd -u 2001 -m -d /home/student2 -s /bin/bash -c "Student User 2" student2

# Giải thích:
# -u 2001: đặt UID
# -m: tạo thư mục home
# -d: chỉ định đường dẫn home
# -s: chỉ định shell
# -c: thêm comment

# 2. Đặt mật khẩu
passwd student2
# Nhập: Student123@

# 3. Kiểm tra
id student2
grep student2 /etc/passwd
```

## Lab 3: Thiết Lập Chính Sách Mật Khẩu

### Tình huống
Cấu hình chính sách mật khẩu cho student1 với các yêu cầu về thời gian.

### Các bước thực hiện
```bash
# 1. Thiết lập chính sách
chage -m 7 -M 90 -W 7 student1

# Giải thích:
# -m 7: tối thiểu 7 ngày mới được đổi mật khẩu
# -M 90: tối đa 90 ngày phải đổi mật khẩu
# -W 7: cảnh báo trước 7 ngày

# 2. Kiểm tra thiết lập
chage -l student1
```

## Lab 4: Sửa Đổi Thông Tin User

### Tình huống
Cần thay đổi thông tin của student1: đổi tên thành student_new và thay đổi shell.

### Các bước thực hiện
```bash
# 1. Đổi tên user
usermod -l student_new student1

# 2. Thay đổi thư mục home
usermod -m -d /home/student_new student_new

# 3. Thay đổi shell
usermod -s /bin/bash student_new

# 4. Kiểm tra thay đổi
grep student_new /etc/passwd
ls -l /home/
```

## Lab 5: Thực Hành Chuyển Đổi User

### Tình huống
Thực hành chuyển đổi giữa các tài khoản khác nhau.

### Các bước thực hiện
```bash
# 1. Chuyển từ root sang student2
su - student2

# 2. Kiểm tra user hiện tại
whoami
pwd

# 3. Thử thực hiện lệnh với quyền root
su -c 'systemctl status sshd' root
# Nhập mật khẩu root khi được yêu cầu

# 4. Quay lại root
exit
```

## Lab 6: Xóa User

### Tình huống
Cần xóa user student2 và dọn dẹp hệ thống.

### Các bước thực hiện
```bash
# 1. Xóa user và thư mục home
userdel -r student2

# 2. Kiểm tra xóa
grep student2 /etc/passwd
ls -l /home/student2
id student2
```

## Xử Lý Lỗi Thường Gặp

1. **Lỗi tạo user**
```bash
# Kiểm tra tên user đã tồn tại
grep username /etc/passwd

# Kiểm tra UID đã được sử dụng
grep UID /etc/passwd
```

2. **Lỗi đổi mật khẩu**
```bash
# Kiểm tra trạng thái mật khẩu
passwd -S username

# Kiểm tra chính sách mật khẩu
chage -l username
```

3. **Lỗi chuyển đổi user**
```bash
# Kiểm tra shell của user
grep username /etc/passwd

# Kiểm tra trạng thái tài khoản
passwd -S username
```

## Bài Tập Thực Hành

1. Tạo user student3 với:
   - UID: 3001
   - Home directory: /home/students/student3
   - Shell: /bin/bash
   - Comment: "Student User 3"

2. Thiết lập chính sách mật khẩu cho student3:
   - Thời gian tối thiểu: 5 ngày
   - Thời gian tối đa: 60 ngày
   - Cảnh báo: 10 ngày

3. Thực hiện các thao tác:
   - Đổi tên student3 thành student_final
   - Thay đổi shell sang /bin/sh
   - Kiểm tra các thay đổi

## Ghi Chú Quan Trọng

1. Luôn kiểm tra lại sau mỗi thao tác
2. Sao lưu trước khi thực hiện thay đổi lớn
3. Sử dụng `man` để xem hướng dẫn chi tiết
4. Ghi nhớ các option quan trọng:
   - `-m`: tạo thư mục home
   - `-r`: xóa home directory khi xóa user
   - `-l`: đổi tên user
   - `-s`: đổi shell

