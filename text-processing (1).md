# Hướng dẫn xử lý văn bản trong Bash

## I. Phân tích yêu cầu

### 1. Input
- Nhập một đoạn văn bản từ người dùng
- Văn bản có thể gồm nhiều dòng

### 2. Output
- Số khoảng cách trong văn bản
- Từ gần cuối của đoạn văn

## II. Các cách giải quyết

### 1. Cách 1: Script đơn giản
```bash
#!/bin/bash

# Tên file: text_analysis_simple.sh
# Mục đích: Phân tích văn bản đơn giản

# Đọc văn bản
echo "Nhập đoạn văn bản (nhấn Ctrl+D khi hoàn thành):"
text=$(cat)

# Đếm khoảng cách
spaces=$(echo "$text" | tr -cd ' ' | wc -c)

# Lấy từ gần cuối
second_last_word=$(echo "$text" | tr -s ' ' '\n' | tail -n 2 | head -n 1)

# In kết quả
echo "Số khoảng cách: $spaces"
echo "Từ gần cuối: $second_last_word"
```

**Giải thích:**
1. `cat` để đọc input từ người dùng
2. `tr -cd ' '` để chỉ giữ lại khoảng trắng
3. `wc -c` để đếm số ký tự
4. Kết hợp `tail` và `head` để lấy từ gần cuối

### 2. Cách 2: Script với xử lý nâng cao
```bash
#!/bin/bash

# Tên file: text_analysis_advanced.sh
# Mục đích: Phân tích văn bản với nhiều tính năng

# Hàm đếm khoảng trắng
count_spaces() {
    local text="$1"
    local count=0
    
    # Sử dụng parameter expansion
    count=${text//[^ ]/}
    echo ${#count}
}

# Hàm lấy từ gần cuối
get_second_last_word() {
    local text="$1"
    # Chuyển văn bản thành mảng các từ
    read -r -a words <<< "$text"
    
    # Lấy độ dài mảng
    local length=${#words[@]}
    
    # Kiểm tra có đủ từ không
    if [ $length -lt 2 ]; then
        echo "Văn bản không đủ từ!"
        return 1
    fi
    
    # Trả về từ gần cuối
    echo "${words[$((length-2))]}"
}

# Nhập văn bản
echo "Nhập đoạn văn bản (nhấn Ctrl+D khi hoàn thành):"
text=$(cat)

# Kiểm tra văn bản không rỗng
if [ -z "$text" ]; then
    echo "Văn bản rỗng!"
    exit 1
fi

# Đếm khoảng trắng
spaces=$(count_spaces "$text")

# Lấy từ gần cuối
second_last=$(get_second_last_word "$text")

# In kết quả
echo "Số khoảng trắng: $spaces"
echo "Từ gần cuối: $second_last"
```

### 3. Cách 3: Script với giao diện và xử lý chi tiết
```bash
#!/bin/bash

# Tên file: text_analysis_full.sh
# Mục đích: Phân tích văn bản với giao diện đẹp và nhiều tính năng

# Định nghĩa màu sắc
GREEN='\033[0;32m'
RED='\033[0;31m'
BLUE='\033[0;34m'
NC='\033[0m'  # No Color

# Hàm đếm khoảng trắng
count_spaces() {
    local text="$1"
    # Đếm tất cả các loại khoảng trắng
    echo "$text" | tr -cd '[:space:]' | wc -c
}

# Hàm phân tích từ
analyze_words() {
    local text="$1"
    # Chuyển văn bản thành mảng
    read -r -a words <<< "$text"
    local word_count=${#words[@]}
    
    echo -e "\n${BLUE}Thông tin về từ:${NC}"
    echo "Tổng số từ: $word_count"
    
    if [ $word_count -ge 2 ]; then
        echo "Từ gần cuối: ${words[$((word_count-2))]}"
        echo "Từ cuối cùng: ${words[$((word_count-1))]}"
    else
        echo -e "${RED}Không đủ từ để phân tích!${NC}"
    fi
}

# Hàm hiển thị thống kê
show_statistics() {
    local text="$1"
    local spaces=$2
    
    echo -e "\n${BLUE}Thống kê chi tiết:${NC}"
    echo "- Độ dài văn bản: ${#text}"
    echo "- Số khoảng trắng: $spaces"
    echo "- Số ký tự (không tính khoảng trắng): $((${#text} - spaces))"
}

# Main script
echo -e "${GREEN}=== Chương trình phân tích văn bản ===${NC}"
echo "Nhập đoạn văn bản (nhấn Ctrl+D khi hoàn thành):"
text=$(cat)

# Kiểm tra văn bản rỗng
if [ -z "$text" ]; then
    echo -e "${RED}Lỗi: Văn bản rỗng!${NC}"
    exit 1
fi

# Thực hiện phân tích
spaces=$(count_spaces "$text")
analyze_words "$text"
show_statistics "$text" "$spaces"
```

## III. Giải thích chi tiết các kỹ thuật

### 1. Đọc input
```bash
text=$(cat)
```
- Sử dụng `cat` để đọc đến EOF (Ctrl+D)
- Lưu toàn bộ input vào biến text

### 2. Đếm khoảng trắng
```bash
tr -cd ' ' | wc -c
```
- `tr`: thay thế hoặc xóa ký tự
- `-cd`: xóa các ký tự không match
- `wc -c`: đếm số ký tự

### 3. Xử lý từ
```bash
read -r -a words <<< "$text"
```
- `read -a`: đọc vào mảng
- `<<<`: here string
- `-r`: không xử lý backslashes

## IV. Hướng dẫn sử dụng

### 1. Tạo và chạy script
```bash
# Tạo file script
vi text_analysis.sh

# Phân quyền thực thi
chmod +x text_analysis.sh

# Chạy script
./text_analysis.sh
```

### 2. Ví dụ sử dụng
```bash
# Input mẫu
This is a sample text
With multiple lines
And some spaces
```

### 3. Test cases
```bash
# Văn bản ngắn
echo "Hello world" | ./text_analysis.sh

# Văn bản nhiều dòng
cat << EOF | ./text_analysis.sh
Line 1
Line 2
Line 3
EOF

# Văn bản rỗng
echo "" | ./text_analysis.sh
```

## V. Best Practices

### 1. Input Handling
- Kiểm tra input rỗng
- Xử lý các loại khoảng trắng
- Normalize input khi cần

### 2. Code Organization
- Tách thành functions
- Comment đầy đủ
- Đặt tên biến rõ ràng

### 3. Output Format
- Hiển thị màu sắc
- Thông báo rõ ràng
- Dễ đọc và hiểu

## VI. Troubleshooting

### 1. Vấn đề encoding
```bash
# Normalize input
text=$(echo "$text" | iconv -f UTF-8 -t UTF-8//IGNORE)
```

### 2. Xử lý khoảng trắng đặc biệt
```bash
# Xử lý tất cả loại khoảng trắng
tr -s '[:space:]' ' '
```

---

*Note: Script này dành cho mục đích học tập. Trong thực tế có thể cần thêm các xử lý khác tùy yêu cầu cụ thể.*