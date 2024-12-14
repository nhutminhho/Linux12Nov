# Bài Thực Hành Quản Lý Người Dùng Trên Linux

## Phần 1: Tạo và Quản Lý User

### Tình huống
Bạn là quản trị viên hệ thống, cần tạo tài khoản cho 3 nhân viên mới: alice, bob và carol.

### Các bước thực hiện:

#### Bước 1: Tạo user mới
```bash
# Đăng nhập với quyền root hoặc sử dụng sudo
sudo -i

# Tạo user alice
useradd -m -s /bin/bash alice
# -m: tạo thư mục home
# -s: chỉ định shell

# Đặt mật khẩu cho alice
passwd alice
# Nhập mật khẩu khi được yêu cầu: Welcome123

# Tạo các user khác
useradd -m -s /bin/bash bob
passwd bob
# Nhập: Welcome123

useradd -m -s /bin/bash carol
passwd carol
# Nhập: Welcome123
```

#### Bước 2: Kiểm tra thông tin user
```bash
# Xem thông tin trong file passwd
grep alice /etc/passwd
grep bob /etc/passwd
grep carol /etc/passwd

# Xem thư mục home
ls -l /home/
```

## Phần 2: Tạo và Quản Lý Group

### Tình huống
Công ty có 2 phòng ban: developers và marketing. Alice và Bob thuộc team developers, Carol thuộc team marketing.

### Các bước thực hiện:

#### Bước 1: Tạo group
```bash
# Tạo group developers
groupadd developers

# Tạo group marketing
groupadd marketing
```

#### Bước 2: Thêm user vào group
```bash
# Thêm alice và bob vào developers
usermod -aG developers alice
usermod -aG developers bob

# Thêm carol vào marketing
usermod -aG marketing carol
```

#### Bước 3: Kiểm tra group
```bash
# Xem thành viên của group
grep developers /etc/group
grep marketing /etc/group

# Kiểm tra group của từng user
groups alice
groups bob
groups carol
```

## Phần 3: Quản Lý File và Thư Mục

### Tình huống
Tạo thư mục dự án chung cho từng team.

### Các bước thực hiện:

#### Bước 1: Tạo thư mục dự án
```bash
# Tạo thư mục cho developers
mkdir -p /opt/projects/dev
chgrp developers /opt/projects/dev
chmod 2775 /opt/projects/dev

# Tạo thư mục cho marketing
mkdir -p /opt/projects/marketing
chgrp marketing /opt/projects/marketing
chmod 2775 /opt/projects/marketing
```

#### Bước 2: Kiểm tra quyền truy cập
```bash
# Kiểm tra quyền
ls -l /opt/projects/

# Thử truy cập với user alice
su - alice
cd /opt/projects/dev
touch test.txt
ls -l test.txt
```

## Phần 4: Thực Hành Khóa và Mở Khóa Tài Khoản

### Tình huống
Bob tạm thời nghỉ phép 1 tháng, cần khóa tài khoản.

### Các bước thực hiện:

#### Bước 1: Khóa tài khoản
```bash
# Khóa tài khoản bob
passwd -l bob

# Kiểm tra trạng thái
passwd -S bob
```

#### Bước 2: Mở khóa tài khoản
```bash
# Khi Bob đi làm lại, mở khóa tài khoản
passwd -u bob

# Kiểm tra trạng thái
passwd -S bob
```

## Phần 5: Xóa User và Group

### Tình huống
Carol chuyển công ty, cần xóa tài khoản.

### Các bước thực hiện:

#### Bước 1: Xóa user
```bash
# Xóa user carol và thư mục home
userdel -r carol

# Kiểm tra xem user đã bị xóa
grep carol /etc/passwd
ls -l /home/carol
```

#### Bước 2: Dọn dẹp group (nếu không cần thiết)
```bash
# Kiểm tra group marketing còn ai không
grep marketing /etc/group

# Nếu không còn ai, có thể xóa group
groupdel marketing
```

## Kiểm Tra Kết Quả

Sau khi hoàn thành các bước trên, kiểm tra lại:

```bash
# 1. Kiểm tra user
cat /etc/passwd | grep -E 'alice|bob|carol'

# 2. Kiểm tra group
cat /etc/group | grep -E 'developers|marketing'

# 3. Kiểm tra thư mục
ls -l /opt/projects/

# 4. Kiểm tra quyền truy cập
su - alice
cd /opt/projects/dev
touch test2.txt
exit

su - bob
cd /opt/projects/dev
ls -l
exit
```

## Xử Lý Lỗi Thường Gặp

1. **Lỗi Permission denied**
   ```bash
   # Kiểm tra quyền
   ls -l /tên_thư_mục
   
   # Sửa quyền nếu cần
   chmod 755 /tên_thư_mục
   ```

2. **User không đăng nhập được**
   ```bash
   # Kiểm tra shell
   grep username /etc/passwd
   
   # Kiểm tra mật khẩu
   passwd -S username
   ```

3. **Không tạo được thư mục home**
   ```bash
   # Tạo thủ công
   mkdir /home/username
   chown username:username /home/username
   chmod 700 /home/username
   ```

## Kết Luận
- Đã tạo và quản lý được user
- Đã tạo và quản lý được group
- Đã thiết lập được quyền truy cập file/thư mục
- Đã thực hành khóa/mở khóa tài khoản
- Đã thực hiện xóa user và group

