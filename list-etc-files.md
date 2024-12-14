# Hướng dẫn liệt kê files trong thư mục /etc

## I. Yêu cầu bài tập
- Kiểm tra thư mục /etc/
- In ra tên file
- Không in tên thư mục

## II. Giải pháp chi tiết

### 1. Sử dụng find (Cách khuyên dùng)

```bash
#!/bin/bash

# Tên file: list_files_find.sh
# Mục đích: Liệt kê các files trong /etc không bao gồm thư mục

# Sử dụng find với các options:
# -maxdepth 1: Chỉ tìm trong thư mục hiện tại
# -type f: Chỉ tìm file thông thường
# -printf "%f\n": In tên file và xuống dòng
find /etc -maxdepth 1 -type f -printf "%f\n"
```

**Giải thích chi tiết:**
1. `find /etc`: Bắt đầu tìm kiếm từ thư mục /etc
2. `-maxdepth 1`: 
   - Chỉ tìm trong thư mục /etc
   - Không đi vào các thư mục con
3. `-type f`:
   - f = regular file (file thông thường)
   - Loại bỏ thư mục, symbolic links, etc
4. `-printf "%f\n"`:
   - %f: chỉ in tên file (không bao gồm path)
   - \n: xuống dòng sau mỗi kết quả

### 2. Sử dụng ls và grep

```bash
#!/bin/bash

# Tên file: list_files_ls.sh
# Mục đích: Liệt kê files trong /etc không bao gồm thư mục

# Sử dụng ls -p để thêm / vào sau thư mục
# Dùng grep -v để loại bỏ các dòng có /
ls -p /etc | grep -v "/"
```

**Giải thích chi tiết:**
1. `ls -p`:
   - -p: thêm dấu / vào cuối tên thư mục
   - Giúp phân biệt file và thư mục
2. `grep -v "/"`:
   - -v: đảo ngược kết quả match
   - Loại bỏ mọi dòng có chứa dấu /
   - Kết quả chỉ còn các file thông thường

### 3. Sử dụng vòng lặp for và test

```bash
#!/bin/bash

# Tên file: list_files_loop.sh
# Mục đích: Liệt kê files trong /etc không bao gồm thư mục

# Duyệt qua từng item trong /etc
for item in /etc/*; do
    # Kiểm tra nếu là file thông thường
    if [ -f "$item" ]; then
        # In ra tên file không bao gồm đường dẫn
        basename "$item"
    fi
done
```

**Giải thích chi tiết:**
1. `for item in /etc/*`:
   - Duyệt qua mọi item trong /etc
   - * là wildcard, match tất cả
2. `[ -f "$item" ]`:
   - -f: test nếu là file thông thường
   - "$item": đường dẫn đầy đủ của item
3. `basename "$item"`:
   - Lấy tên file từ đường dẫn
   - Loại bỏ phần path phía trước

## III. Hướng dẫn sử dụng

### 1. Tạo và chạy script

```bash
# Tạo file script
vi list_files.sh

# Thêm quyền thực thi
chmod +x list_files.sh

# Chạy script
./list_files.sh
```

### 2. So sánh kết quả các cách

```bash
# So sánh kết quả của các cách
diff <(./list_files_find.sh) <(./list_files_ls.sh)
```

## IV. So sánh các phương pháp

### 1. Sử dụng find
**Ưu điểm:**
- Performance tốt nhất
- Nhiều options tùy chỉnh
- Xử lý được file names đặc biệt

**Nhược điểm:**
- Syntax phức tạp
- Khó nhớ các options

### 2. Sử dụng ls và grep
**Ưu điểm:**
- Syntax đơn giản
- Dễ hiểu, dễ nhớ
- Nhanh với ít files

**Nhược điểm:**
- Có thể gặp vấn đề với tên file đặc biệt
- Performance kém hơn find
- Không có nhiều options tùy chỉnh

### 3. Sử dụng vòng lặp for
**Ưu điểm:**
- Code rõ ràng, dễ đọc
- Dễ thêm logic xử lý
- Linh hoạt trong tùy chỉnh

**Nhược điểm:**
- Performance kém nhất
- Code dài hơn
- Phức tạp khi cần thêm điều kiện

## V. Best Practices

### 1. Chọn phương pháp
- Dùng `find` cho production và performance
- Dùng `ls + grep` cho scripts đơn giản
- Dùng `for loop` khi cần xử lý thêm

### 2. Error Handling
```bash
#!/bin/bash

# Kiểm tra quyền truy cập /etc
if [ ! -r "/etc" ]; then
    echo "Không có quyền đọc /etc"
    exit 1
fi

# Thực hiện liệt kê files
find /etc -maxdepth 1 -type f -printf "%f\n"
```

### 3. Performance Optimization
```bash
#!/bin/bash

# Cache kết quả nếu cần sử dụng nhiều lần
files=$(find /etc -maxdepth 1 -type f -printf "%f\n")
echo "$files"
```

## VI. Troubleshooting

### 1. Permission Issues
```bash
# Kiểm tra quyền
ls -l /etc

# Chạy với sudo nếu cần
sudo ./list_files.sh
```

### 2. Special Characters
```bash
# Xử lý tên file có ký tự đặc biệt
find /etc -maxdepth 1 -type f -print0 | xargs -0 basename
```

---

*Note: Script này dành cho mục đích học tập. Trong môi trường thực tế cần thêm error handling và security checks.*