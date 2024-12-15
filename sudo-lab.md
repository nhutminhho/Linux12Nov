# Bài Lab: Phân Quyền Sudo cho User Admin trên AlmaLinux 9

## Giới thiệu

Bài lab này tập trung vào việc phân quyền sudo cho một user tên admin mà không thuộc group wheel, với hai trường hợp khác nhau. Chúng ta sẽ tìm hiểu cách cấu hình sudo một cách chi tiết và an toàn.

## Yêu cầu ban đầu

1. Tạo user admin không nằm trong group wheel:
```bash
sudo useradd admin
sudo passwd admin
```

## Trường hợp 1: Phân quyền cụ thể

### Yêu cầu
- User admin cần có quyền:
  - Tắt hệ thống
  - Tạo user
  - Xóa user
  - Đặt password

### Giải thích cấu hình

```bash
# Định nghĩa các nhóm lệnh (Command Aliases)
Cmnd_Alias TATHETHONG = /sbin/poweroff, /sbin/shutdown, /usr/sbin/poweroff, /usr/sbin/shutdown
Cmnd_Alias TAOUSER = /sbin/useradd, /bin/passwd, /sbin/userdel, /usr/sbin/useradd, /usr/bin/passwd, /usr/sbin/userdel
```

#### Phân tích từng phần:

1. `Cmnd_Alias TATHETHONG`:
   - Nhóm các lệnh liên quan đến việc tắt hệ thống
   - Bao gồm cả đường dẫn /sbin và /usr/sbin vì một số distro có thể đặt lệnh ở vị trí khác nhau

2. `Cmnd_Alias TAOUSER`:
   - Nhóm các lệnh liên quan đến quản lý user
   - Bao gồm useradd, passwd, và userdel
   - Cũng bao gồm cả hai đường dẫn có thể có

### Cấu hình cho user admin
```bash
admin  ALL=(ALL)  TAOUSER, TATHETHONG
```

## Trường hợp 2: Phân quyền toàn bộ trừ một số lệnh

### Yêu cầu
- User admin có toàn bộ quyền của root
- Không được sử dụng:
  - visudo
  - usermod
  - Không được tự thêm vào group wheel

### Giải thích cấu hình

```bash
Cmnd_Alias CAMQUYEN = /sbin/visudo, /usr/sbin/visudo, /usr/sbin/usermod, /sbin/useradd -G wheel *****, /usr/sbin/useradd -G wheel ***
admin   ALL=(ALL)       ALL, !CAMQUYEN
```

#### Phân tích chi tiết:

1. `Cmnd_Alias CAMQUYEN`:
   - Định nghĩa các lệnh bị cấm
   - Sử dụng `*****` để ngăn chặn mọi cách sử dụng lệnh với group wheel
   - Bao gồm cả visudo và usermod để ngăn chặn việc sửa đổi quyền

2. `admin ALL=(ALL) ALL, !CAMQUYEN`:
   - `ALL`: Cho phép tất cả các lệnh
   - `!CAMQUYEN`: Từ chối các lệnh trong nhóm CAMQUYEN
   - Dấu ! là toán tử phủ định trong sudoers

### Lưu ý quan trọng

1. Về đường dẫn `secure_path`:
```bash
Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin
```
- Định nghĩa các đường dẫn mặc định cho sudo
- Nếu có dòng này, chỉ cần một trong hai đường dẫn (/sbin hoặc /usr/sbin)
- Nếu không có, cần định nghĩa cả hai để đảm bảo tính tương thích

2. Về bảo mật:
- Luôn sử dụng đường dẫn tuyệt đối
- Cẩn thận với wildcard (*)
- Kiểm tra cấu hình trước khi áp dụng

## Cách kiểm tra

1. Kiểm tra quyền của user admin:
```bash
sudo -l -U admin
```

2. Thử nghiệm các lệnh:
```bash
# Đăng nhập với user admin
su - admin

# Thử các lệnh được phép
sudo poweroff
sudo useradd testuser

# Thử các lệnh bị cấm
sudo visudo  # Sẽ bị từ chối
```

## Kết luận

Bài lab này minh họa cách:
- Sử dụng Command Aliases để nhóm các lệnh
- Phân quyền chi tiết cho user
- Sử dụng toán tử phủ định (!) để hạn chế quyền
- Xử lý các vấn đề về đường dẫn trong sudo