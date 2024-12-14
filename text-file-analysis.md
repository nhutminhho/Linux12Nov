# Hướng dẫn xử lý file text: Đếm dòng và hiển thị dòng cuối

## I. Phân tích yêu cầu

### 1. Input
- Đường dẫn đến file text
- Cần kiểm tra file có tồn tại không
- Kiểm tra quyền đọc file

### 2. Output
- Số dòng trong file
- Nội dung dòng cuối

## II. Các cách giải quyết

### 1. Cách 1: Script đơn giản nhất
```bash
#!/bin/bash

# Tên file: check_file_simple.sh
# Mục đích: Đếm dòng và hiển thị dòng cuối của file

# Kiểm tra tham số dòng lệnh
if [ $# -ne 1 ]; then
    echo "Cách sử dụng: $0 <tên_file>"
    exit 1
fi

# Đếm số dòng
wc -l < "$1"

# Hiển thị dòng cuối
tail -n 1 "$1"
```

**Giải thích:**
- Sử dụng wc -l để đếm dòng
- Sử dụng tail -n 1 để lấy dòng cuối
- Script đơn giản nhưng không có error handling

### 2. Cách 2: Script với error handling
```bash
#!/bin/bash

# Tên file: check_file_full.sh
# Mục đích: Đếm dòng và hiển thị dòng cuối với xử lý lỗi

# Kiểm tra tham số đầu vào
if [ $# -ne 1 ]; then
    echo "Cách sử dụng: $0 <tên_file>"
    exit 1
fi

# Lưu tên file vào biến
file="$1"

# Kiểm tra file có tồn tại không
if [ ! -f "$file" ]; then
    echo "Error: File '$file' không tồn tại!"
    exit 1
fi

# Kiểm tra quyền đọc
if [ ! -r "$file" ]; then
    echo "Error: Không có quyền đọc file '$file'!"
    exit 1
fi

# Đếm số dòng
line_count=$(wc -l < "$file")
echo "Số dòng trong file: $line_count"

# Kiểm tra file có rỗng không
if [ $line_count -eq 0 ]; then
    echo "File rỗng!"
    exit 0
fi

# Hiển thị dòng cuối
echo "Nội dung dòng cuối:"
tail -n 1 "$file"
```

**Giải thích chi tiết:**

1. **Kiểm tra tham số:**
   ```bash
   if [ $# -ne 1 ]; then
   ```
   - $# là số lượng tham số dòng lệnh
   - -ne 1 kiểm tra không bằng 1
   - Đảm bảo script được gọi với đúng một tham số

2. **Kiểm tra file:**
   ```bash
   if [ ! -f "$file" ]; then
   ```
   - -f kiểm tra file có tồn tại và là regular file
   - ! phủ định điều kiện

3. **Kiểm tra quyền:**
   ```bash
   if [ ! -r "$file" ]; then
   ```
   - -r kiểm tra quyền đọc
   - Đảm bảo có thể đọc file

### 3. Cách 3: Script với nhiều tính năng
```bash
#!/bin/bash

# Tên file: check_file_advanced.sh
# Mục đích: Phân tích file text với nhiều tính năng

# Định nghĩa màu sắc
GREEN='\033[0;32m'
RED='\033[0;31m'
NC='\033[0m'  # No Color

# Hàm hiển thị usage
show_usage() {
    echo "Cách sử dụng: $0 <tên_file>"
    echo "  Chức năng: Đếm số dòng và hiển thị dòng cuối của file text"
}

# Hàm kiểm tra file
check_file() {
    local file="$1"
    
    # Kiểm tra file tồn tại
    if [ ! -f "$file" ]; then
        echo -e "${RED}Error: File '$file' không tồn tại!${NC}"
        return 1
    fi
    
    # Kiểm tra quyền đọc
    if [ ! -r "$file" ]; then
        echo -e "${RED}Error: Không có quyền đọc file '$file'!${NC}"
        return 1
    fi
    
    return 0
}

# Hàm đếm dòng
count_lines() {
    local file="$1"
    local count=$(wc -l < "$file")
    echo -e "${GREEN}Số dòng trong file:${NC} $count"
    return $count
}

# Hàm hiển thị dòng cuối
show_last_line() {
    local file="$1"
    echo -e "${GREEN}Nội dung dòng cuối:${NC}"
    tail -n 1 "$file"
}

# Main script
main() {
    # Kiểm tra số lượng tham số
    if [ $# -ne 1 ]; then
        show_usage
        exit 1
    fi
    
    local file="$1"
    
    # Kiểm tra file
    if ! check_file "$file"; then
        exit 1
    fi
    
    # Thực hiện đếm dòng
    count_lines "$file"
    local line_count=$?
    
    # Kiểm tra file rỗng
    if [ $line_count -eq 0 ]; then
        echo -e "${RED}File rỗng!${NC}"
        exit 0
    fi
    
    # Hiển thị dòng cuối
    show_last_line "$file"
}

# Chạy script
main "$@"
```

## III. Hướng dẫn sử dụng

### 1. Tạo file test
```bash
# Tạo file test.txt với nội dung mẫu
cat > test.txt << EOF
Dòng 1
Dòng 2
Dòng 3
Đây là dòng cuối
EOF
```

### 2. Chạy script
```bash
# Phân quyền thực thi
chmod +x check_file_advanced.sh

# Chạy script
./check_file_advanced.sh test.txt
```

### 3. Test cases
```bash
# Test file không tồn tại
./check_file_advanced.sh nonexistent.txt

# Test file rỗng
touch empty.txt
./check_file_advanced.sh empty.txt

# Test file không có quyền đọc
chmod 000 noperm.txt
./check_file_advanced.sh noperm.txt
```

## IV. Các lưu ý quan trọng

### 1. Performance
- `wc -l` hiệu quả cho việc đếm dòng
- `tail -n 1` tối ưu cho việc lấy dòng cuối
- Không cần đọc toàn bộ file vào memory

### 2. Security
- Luôn kiểm tra input
- Xử lý tên file có ký tự đặc biệt
- Kiểm tra quyền truy cập

### 3. Maintainability
- Code có cấu trúc rõ ràng
- Comment đầy đủ
- Dễ mở rộng thêm tính năng

## V. Best Practices

### 1. Error Handling
```bash
# Xử lý lỗi chi tiết
if ! some_command; then
    echo "Error: Command failed"
    exit 1
fi
```

### 2. Input Validation
```bash
# Kiểm tra input kỹ lưỡng
if [[ "$input" =~ [^[:print:]] ]]; then
    echo "Input contains non-printable characters"
    exit 1
fi
```

### 3. Output Format
```bash
# Format output dễ đọc
printf "%-20s: %d\n" "Số dòng" "$count"
```

---

*Note: Scripts trên dành cho mục đích học tập. Trong môi trường thực tế cần thêm các xử lý bảo mật khác.*