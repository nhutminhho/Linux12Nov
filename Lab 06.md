# Thực Hành Quản Lý Group Trên Linux

## Mục Tiêu
- Tạo và quản lý group
- Thêm user vào group
- Sửa đổi thông tin group
- Xóa group

## Chuẩn Bị
1. Máy chủ Linux (AlmaLinux/CentOS)
2. Quyền root hoặc sudo
3. Terminal để thực hành

## Lab 1: Tạo Group Cơ Bản

### Tình huống
Công ty cần tạo các nhóm cho phòng kế toán và nhân sự.

### Các bước thực hiện
```bash
# 1. Đăng nhập với quyền root
sudo -i

# 2. Tạo group kế toán với GID cụ thể
groupadd -g 5000 accounting

# 3. Tạo group nhân sự
groupadd -g 5001 hr

# 4. Kiểm tra các group đã tạo
grep accounting /etc/group
grep hr /etc/group
```

## Lab 2: Tạo User và Thêm Vào Group

### Tình huống
Thêm nhân viên mới vào các phòng ban.

### Các bước thực hiện
```bash
# 1. Tạo user cho nhân viên kế toán
useradd -m accountant1
passwd accountant1
# Đặt mật khẩu: Acc123@

# 2. Tạo user cho nhân viên HR
useradd -m hr1
passwd hr1
# Đặt mật khẩu: Hr123@

# 3. Thêm user vào group chính
usermod -g accounting accountant1
usermod -g hr hr1

# 4. Thêm accountant1 vào group phụ hr
usermod -a -G hr accountant1

# 5. Kiểm tra thành viên group
grep accounting /etc/group
groups accountant1
id accountant1
```

## Lab 3: Sửa Đổi Group

### Tình huống
Cần đổi tên group và GID theo yêu cầu mới.

### Các bước thực hiện
```bash
# 1. Đổi tên group accounting thành finance
groupmod -n finance accounting

# 2. Đổi GID của group finance
groupmod -g 6000 finance

# 3. Kiểm tra thay đổi
grep finance /etc/group
grep hr /etc/group

# 4. Kiểm tra ảnh hưởng đến user
id accountant1
```

## Lab 4: Quản Lý Group Đặc Biệt

### Tình huống
Tạo group dự án với cùng GID.

### Các bước thực hiện
```bash
# 1. Tạo group project1 với GID 7000
groupadd -g 7000 project1

# 2. Tạo group project2 với cùng GID
groupadd -o -g 7000 project2

# 3. Kiểm tra cấu hình
grep project /etc/group

# 4. Thêm user vào các group dự án
usermod -a -G project1,project2 accountant1
usermod -a -G project1 hr1

# 5. Kiểm tra thành viên
groups accountant1
groups hr1
```

## Lab 5: Xóa Group

### Tình huống
Dự án đã hoàn thành, cần xóa các group không còn sử dụng.

### Các bước thực hiện
```bash
# 1. Kiểm tra thành viên group trước khi xóa
grep project1 /etc/group
grep project2 /etc/group

# 2. Xóa group project2
groupdel project2

# 3. Kiểm tra lại
grep project2 /etc/group
groups accountant1
```

## Bài Tập Thực Hành

### Bài tập 1: Tạo Cấu Trúc Nhóm Cho Dự Án
1. Tạo group dev_team (GID: 8000)
2. Tạo group test_team (GID: 8001)
3. Tạo user dev1 và test1
4. Thêm dev1 vào dev_team và test1 vào test_team
5. Thêm dev1 vào test_team như group phụ

### Bài tập 2: Quản Lý Group
1. Đổi tên dev_team thành development
2. Thay đổi GID của development thành 8500
3. Kiểm tra ảnh hưởng đến user

## Xử Lý Lỗi Thường Gặp

### 1. Lỗi GID đã tồn tại
```bash
# Kiểm tra GID đã sử dụng
grep "8000" /etc/group

# Sử dụng GID khác hoặc -o để cho phép dùng chung
groupadd -o -g 8000 new_group
```

### 2. Lỗi Không Thể Xóa Group
```bash
# Kiểm tra xem có user nào đang dùng làm primary group
grep "group_name" /etc/passwd

# Đổi primary group của user trước khi xóa
usermod -g other_group username
```

### 3. Lỗi Thêm User Vào Group
```bash
# Kiểm tra group tồn tại
grep "group_name" /etc/group

# Sử dụng -a để tránh mất group hiện có
usermod -a -G new_group username
```

## Ghi Chú Quan Trọng

1. **Quy tắc đặt tên group**
   - Sử dụng ký tự chữ và số
   - Tránh ký tự đặc biệt
   - Tên có ý nghĩa với mục đích sử dụng

2. **Quản lý GID**
   - GID nhỏ hơn 1000 thường dành cho system groups
   - Sử dụng GID lớn hơn 1000 cho user groups
   - Lưu ý khi dùng chung GID (-o option)

3. **Kiểm tra sau thay đổi**
   - Luôn kiểm tra /etc/group
   - Xác nhận quyền truy cập của user
   - Kiểm tra cả primary và secondary groups

