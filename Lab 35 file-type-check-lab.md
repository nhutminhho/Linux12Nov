# Lab Thực Hành: Kiểm Tra Loại File Trong Linux

## Phần 1: Hiểu Script Cơ Bản

### 1.1. Cấu Trúc Script
```bash
#!/bin/bash

# Giải thích:
- Dòng đầu tiên là shebang, chỉ định shell thực thi script
- Bash là shell phổ biến nhất trong Linux
```

### 1.2. Nhận Input
```bash
echo "Input name of file you wanna check: "
read FILE

# Giải thích:
- echo: hiển thị thông báo cho người dùng
- read: đọc input từ bàn phím
- FILE: biến lưu tên file/thư mục cần kiểm tra
```

## Phần 2: Thực Hành Kiểm Tra File

### Bài 1: Script Cơ Bản
```bash
#!/bin/bash

# Nhận input từ người dùng
echo "Input name of file you wanna check: "
read FILE

# Kiểm tra có phải thư mục không
if [ -d "$FILE" ]; then
    echo "Your is directory"
    ls -la "$FILE"
else
    file "$FILE"
    echo "Your file is not directory"
fi

# Giải thích từng phần:
1. [ -d "$FILE" ]: kiểm tra xem có phải thư mục không
2. ls -la: liệt kê chi tiết nội dung thư mục
3. file: hiển thị loại file
```

### Bài 2: Tạo File Test
```bash
# 1. Tạo thư mục test
mkdir test_dir

# 2. Tạo file thường 
echo "Hello" > test_file.txt

# 3. Chạy script với cả hai loại
./check_file.sh
# Nhập: test_dir
# Sau đó chạy lại
# Nhập: test_file.txt

# Giải thích:
- Tạo các loại file khác nhau để test
- Kiểm tra cách script xử lý từng loại
```

## Phần 3: Mở Rộng Script

### Bài 1: Thêm Kiểm Tra Chi Tiết
```bash
#!/bin/bash

echo "Input name of file you wanna check: "
read FILE

# Kiểm tra file có tồn tại không
if [ ! -e "$FILE" ]; then
    echo "File/Directory does not exist!"
    exit 1
fi

# Kiểm tra loại
if [ -d "$FILE" ]; then
    echo "This is a directory"
    echo "Contents:"
    ls -la "$FILE"
    echo "Total size: $(du -sh "$FILE" | cut -f1)"
elif [ -f "$FILE" ]; then
    echo "This is a regular file"
    echo "File info:"
    file "$FILE"
    echo "Size: $(ls -lh "$FILE" | awk '{print $5}')"
else
    echo "This is a special file"
    file "$FILE"
fi

# Giải thích thêm:
- -e: kiểm tra tồn tại
- -f: kiểm tra file thường
- du -sh: hiển thị dung lượng
```

## Phần 4: Bài Tập Thực Hành

### Bài 1: Thực Hành Với Các Loại File
```bash
# Tạo các loại file khác nhau để test
mkdir dir1
echo "text" > file1.txt
ln -s file1.txt link1

# Chạy script với từng loại
./check_file.sh
# Test với từng file vừa tạo
```

### Bài 2: Script Nâng Cao
```bash
#!/bin/bash

# Menu lựa chọn
echo "1. Check single file"
echo "2. Check all files in current directory"
read -p "Choose option (1/2): " option

case $option in
    1)
        echo "Input name of file: "
        read FILE
        # Code kiểm tra file như trên
        ;;
    2)
        echo "Checking all files in current directory:"
        for FILE in *; do
            echo "=== $FILE ==="
            if [ -d "$FILE" ]; then
                echo "Directory"
                ls -l "$FILE" | wc -l
            else
                file "$FILE"
            fi
        done
        ;;
    *)
        echo "Invalid option"
        ;;
esac
```

## Tips và Lưu Ý

### 1. Kiểm Tra An Toàn
```bash
# Luôn kiểm tra file tồn tại
[ ! -e "$FILE" ] && echo "Not found" && exit 1

# Kiểm tra quyền truy cập
[ ! -r "$FILE" ] && echo "Cannot read" && exit 1
```

### 2. Best Practices
1. **Dùng Dấu Nháy Kép:**
   ```bash
   # Đúng
   [ -d "$FILE" ]
   
   # Sai
   [ -d $FILE ]
   ```

2. **Error Handling:**
   ```bash
   if ! command; then
       echo "Error occurred"
       exit 1
   fi
   ```

3. **Documentation:**
   ```bash
   # Thêm comment giải thích
   # Ghi rõ mục đích mỗi phần
   ```

### 3. Testing
```bash
# Test với các tình huống:
- Tên file có khoảng trắng
- File không tồn tại
- File đặc biệt
- Symbolic links
```

