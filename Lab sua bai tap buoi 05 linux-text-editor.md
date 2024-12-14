# Hướng dẫn giải bài tập Text Editor

## I. Phân tích yêu cầu

### 1. Các thao tác cần thực hiện:
- Tạo và quản lý thư mục
- Sử dụng text editor (vi)
- Copy và điều chỉnh nội dung file
- Thao tác với file system

### 2. Yêu cầu đầu ra:
- File cuối cùng có nội dung được lặp lại theo mẫu
- Các file được lưu đúng vị trí
- Thực hiện theo trình tự các bước

## II. Các bước thực hiện

### Bước 1: Tạo thư mục (nếu chưa có)
```bash
# Tạo thư mục u1 và u2
mkdir -p /home/u1 /home/u2
```

*Giải thích:*
- `mkdir`: Lệnh tạo thư mục
- `-p`: Tạo cả thư mục cha nếu chưa tồn tại
- Tạo cùng lúc 2 thư mục tiết kiệm thời gian

### Bước 2: Tạo file đầu tiên
```bash
# Tạo và mở file với vi
vi /home/u1/rememberWhen.txt
```

*Trong vi editor:*
1. Nhấn `i` để vào chế độ insert
2. Copy/paste đoạn văn bản gốc:
```text
Remember when I was young and so were you
And time stood still and love was all we knew
You were the first, so was I
We made love and then you cried
Remember when
Remember when we vowed the vows and walked the walk
Gave our hearts, made the start, it was hard
We lived and learned, life threw curves
There was joy, there was hurt
Remember when
```
3. Nhấn `ESC` để thoát chế độ insert
4. Gõ `:w Remember.conf` để lưu với tên mới
5. Gõ `:q` để thoát

*Giải thích:*
- Dùng vi vì có sẵn trên hệ thống
- `i` để insert text là cách nhanh nhất
- Lưu với `:w` cho phép đổi tên file ngay lập tức
- Thoát với `:q` vì không cần chỉnh sửa gì thêm

### Bước 3: Copy file sang thư mục mới
```bash
cp /home/u1/Remember.conf /home/u2/lyricsRememberWhen.txt
```

*Giải thích:*
- `cp`: Lệnh copy file đơn giản và nhanh
- Copy cả file và đổi tên trong một lệnh
- Không cần tạo file mới rồi paste nội dung

### Bước 4: Chỉnh sửa file cuối cùng

```bash
# Mở file để edit
vi /home/u2/lyricsRememberWhen.txt
```

*Các bước trong vi:*
1. Đưa con trỏ đến đầu file (gg)
2. Thực hiện copy toàn bộ nội dung:
   - `gg` để về đầu file
   - `yG` để copy từ vị trí hiện tại đến cuối file
3. Nhấn `p` để paste 1 lần
4. Lặp lại `p` để paste thêm lần nữa

*Giải thích cách làm:*
1. `gg`: Di chuyển nhanh về đầu file
2. `yG`: Copy toàn bộ nội dung một lần
3. `p`: Paste nhanh và chính xác
4. Lặp lại paste để có nội dung theo yêu cầu

### Bước 5: Lưu và thoát
```bash
# Trong vi
:wq
```

*Giải thích:*
- `:wq`: Lưu file và thoát trong một lệnh
- Không cần tách thành hai lệnh `:w` và `:q`

## III. Lý do chọn cách làm này

### 1. Sử dụng vi editor:
- Có sẵn trên mọi hệ thống Linux
- Thao tác nhanh với các lệnh đơn giản
- Không cần cài đặt thêm phần mềm
- Phù hợp cho cả file nhỏ và lớn

### 2. Thao tác copy/paste trong vi:
- Nhanh hơn gõ lại toàn bộ văn bản
- Tránh được lỗi chính tả
- Dễ thực hiện với lệnh đơn giản
- Giữ được định dạng chính xác

### 3. Sử dụng lệnh cp:
- Đơn giản và dễ nhớ
- Copy file nhanh và chính xác
- Có thể đổi tên file đích
- Không cần mở editor

### 4. Phương pháp paste trong vi:
- Nhanh hơn copy từ bên ngoài
- Giữ được format chính xác
- Không bị lỗi encoding
- Dễ dàng lặp lại nội dung

## IV. Tips và lưu ý

### 1. Kiểm tra trước khi thực hiện:
```bash
# Kiểm tra thư mục
ls -la /home

# Kiểm tra quyền
whoami
```

### 2. Backup nếu cần:
```bash
# Backup file trước khi chỉnh sửa
cp /home/u2/lyricsRememberWhen.txt /home/u2/lyricsRememberWhen.txt.bak
```

### 3. Verify sau khi hoàn thành:
```bash
# Kiểm tra nội dung file
cat /home/u2/lyricsRememberWhen.txt

# Kiểm tra số dòng
wc -l /home/u2/lyricsRememberWhen.txt
```

## V. Troubleshooting

### 1. Nếu không có quyền:
```bash
# Kiểm tra quyền
ls -l /home/u1 /home/u2

# Thêm quyền nếu cần
sudo chmod 755 /home/u1 /home/u2
```

### 2. Nếu gặp lỗi khi copy:
```bash
# Kiểm tra disk space
df -h

# Kiểm tra file đích
ls -l /home/u2/lyricsRememberWhen.txt
```

## VI. Tối ưu hóa

1. **Sử dụng alias:**
```bash
# Thêm vào ~/.bashrc
alias vi='vim'
```

2. **Tạo backup tự động:**
```bash
# Trước khi edit
cp lyricsRememberWhen.txt{,.bak}
```

---

*Note: Hướng dẫn này tập trung vào cách giải quyết đơn giản và hiệu quả nhất. Trong thực tế có thể có nhiều cách khác tùy theo yêu cầu cụ thể.*