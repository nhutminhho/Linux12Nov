# Lab Thực Hành: Xử Lý Văn Bản Trong Bash

## Giải Pháp 1: Sử Dụng Biến

```bash
#!/bin/bash

# Thông báo nhập văn bản
echo "Nhập đoạn văn bản (nhấn Ctrl+D khi kết thúc):"

# Đọc văn bản từ input
text=$(cat)

# Giải thích:
# - cat: đọc input từ bàn phím
# - Ctrl+D: kết thúc nhập

# Đếm số khoảng trắng
spaces=$(echo "$text" | tr -cd ' ' | wc -c)
echo "Số khoảng trắng: $spaces"

# Giải thích:
# - tr -cd ' ': giữ lại chỉ khoảng trắng
# - wc -c: đếm số ký tự

# Lấy từ gần cuối
second_last_word=$(echo "$text" | tr -s ' ' '\n' | tail -n 2 | head -n 1)
echo "Từ gần cuối: $second_last_word"

# Giải thích:
# - tr -s ' ' '\n': thay khoảng trắng bằng xuống dòng
# - tail -n 2: lấy 2 dòng cuối
# - head -n 1: lấy dòng đầu tiên (trong 2 dòng cuối)
```

## Giải Pháp 2: Sử Dụng Arrays

```bash
#!/bin/bash

# Đọc văn bản vào mảng
echo "Nhập đoạn văn bản (nhấn Ctrl+D khi kết thúc):"
read -d '' text

# Chuyển văn bản thành mảng các từ
IFS=' ' read -ra words <<< "$text"

# Giải thích:
# - IFS=' ': đặt dấu phân cách là khoảng trắng
# - read -ra: đọc vào mảng

# Đếm khoảng trắng
space_count=$(echo "$text" | grep -o " " | wc -l)
echo "Số khoảng trắng: $space_count"

# Lấy từ gần cuối
length=${#words[@]}
if [ $length -gt 1 ]; then
    echo "Từ gần cuối: ${words[$length-2]}"
else
    echo "Văn bản quá ngắn!"
fi
```

## Giải Pháp 3: Sử Dụng Sed và Awk

```bash
#!/bin/bash

echo "Nhập đoạn văn bản (nhấn Ctrl+D khi kết thúc):"
text=$(cat)

# Đếm khoảng trắng sử dụng awk
spaces=$(echo "$text" | awk -F'' '{
    count=0
    for(i=1; i<=NF; i++) 
        if($i==" ") count++
    print count
}')
echo "Số khoảng trắng: $spaces"

# Lấy từ gần cuối sử dụng awk
second_last=$(echo "$text" | awk '{
    if (NF > 1) 
        print $(NF-1)
    else 
        print "Văn bản quá ngắn!"
}')
echo "Từ gần cuối: $second_last"

# Giải thích:
# - awk -F'': đặt field separator là ký tự
# - NF: số field (từ) trong dòng
# - $(NF-1): từ gần cuối
```

## Test và Kiểm Tra

### Test Case 1: Văn bản đơn giản
```bash
Input: "Hello world from bash script"
Expected output:
- Số khoảng trắng: 3
- Từ gần cuối: from
```

### Test Case 2: Nhiều khoảng trắng
```bash
Input: "This   has   multiple    spaces"
Expected output:
- Số khoảng trắng: 9
- Từ gần cuối: multiple
```

### Test Case 3: Một từ
```bash
Input: "Hello"
Expected output:
- Số khoảng trắng: 0
- Văn bản quá ngắn!
```

## Xử Lý Đặc Biệt

### 1. Xử Lý Khoảng Trắng Đặc Biệt
```bash
# Xử lý tab và newline
spaces=$(echo "$text" | tr -s '[:space:]' '\n' | grep -c '^$')

# Giải thích:
# - [:space:]: bao gồm tất cả loại khoảng trắng
# - tr -s: squeeze các khoảng trắng liên tiếp
```

### 2. Normalize Text
```bash
# Chuẩn hóa văn bản
normalized=$(echo "$text" | tr -s ' ' | sed 's/^ *//;s/ *$//')

# Giải thích:
# - tr -s ' ': gộp khoảng trắng liên tiếp
# - sed: xóa khoảng trắng đầu và cuối
```

## Tips và Best Practices

### 1. Input Validation
```bash
# Kiểm tra input rỗng
if [ -z "$text" ]; then
    echo "Vui lòng nhập văn bản!"
    exit 1
fi
```

### 2. Error Handling
```bash
# Kiểm tra lỗi trong xử lý
if ! command; then
    echo "Có lỗi xảy ra!"
    exit 1
fi
```

### 3. Performance
```bash
# Với văn bản dài, sử dụng awk hiệu quả hơn
# Tránh nhiều pipe (|) không cần thiết
# Sử dụng biến thay vì subshell khi có thể
```

### 4. Maintainability
```bash
# Thêm comment giải thích
# Chia nhỏ xử lý phức tạp
# Sử dụng tên biến có ý nghĩa
```

