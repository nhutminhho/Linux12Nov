# Lab Thực Hành: Xử Lý Tham Số Dòng Lệnh Trong Bash

## Phần 1: Giải Thích Script

### 1.1 Cấu Trúc Cơ Bản
```bash
#!/bin/bash

# Giải thích:
# - Shebang line chỉ định shell thực thi
# - Script này sẽ xử lý 2 loại tham số:
#   + -h hoặc --help: hiển thị hướng dẫn
#   + -f hoặc --file: phân tích file
```

### 1.2 Vòng Lặp While Chính
```bash
while [ "$#" -gt 0 ]; do
    # Code xử lý
done

# Giải thích:
# - $# là số lượng tham số còn lại
# - -gt 0 kiểm tra còn tham số không
# - Vòng lặp sẽ chạy cho đến khi hết tham số
```

## Phần 2: Thực Hành Cơ Bản

### Bài 1: Tạo File Test
```bash
# Tạo file test.txt
cat > test.txt << 'EOF'
Dòng 1
Dòng 2
Dòng thứ 3 với nhiều từ hơn
EOF

# Giải thích:
# - Tạo file với nội dung mẫu
# - Dùng để test script
```

### Bài 2: Script Hoàn Chỉnh
```bash
#!/bin/bash

# Vòng lặp xử lý tham số
while [ "$#" -gt 0 ]; do
    # Switch case cho các tham số
    case "$1" in
        # Trường hợp help
        -h|--help )
            echo "USAGE: $0 [-h] [--help] [-f file] [--file file]"
            shift
            exit 1
            ;;
            
        # Trường hợp file
        -f|--file )
            FILE=$2
            # Kiểm tra file tồn tại
            if ! [ -f "$FILE" ]; then
                echo "File does not exist"
                exit 2
            fi
            
            # Đếm các thông số
            LINES=$(cat "$FILE" | wc -l)
            WORDS=$(cat "$FILE" | wc -w)
            CHARACTERS=$(cat "$FILE" | wc -m)
            
            # In kết quả
            echo "==FILE: $FILE=="
            echo "Lines: $LINES"
            echo "Words: $WORDS"
            echo "Characters: $CHARACTERS"
            
            shift
            shift
            ;;
            
        # Trường hợp không hợp lệ
        * )
            echo "USAGE: $0 [-h] [--help] [-f file] [--file file]"
            exit 1
            ;;
    esac
done

# Giải thích từng phần:
# 1. Case statement:
#    - Xử lý các trường hợp tham số khác nhau
#    - | cho phép nhiều pattern (-h|--help)
#
# 2. Shift command:
#    - Loại bỏ tham số đã xử lý
#    - shift 2 lần với -f vì cần bỏ cả -f và tên file
#
# 3. File checks:
#    - -f kiểm tra file có tồn tại không
#    - exit 2 khi file không tồn tại
#
# 4. Word count:
#    - wc -l: đếm số dòng
#    - wc -w: đếm số từ
#    - wc -m: đếm số ký tự
```

## Phần 3: Thực Hành Nâng Cao

### Bài 1: Thêm Tính Năng
```bash
#!/bin/bash

# Thêm biến để theo dõi có tham số nào được xử lý chưa
PROCESSED=0

while [ "$#" -gt 0 ]; do
    case "$1" in
        -h|--help )
            echo "USAGE: $0 [-h] [--help] [-f file] [--file file]"
            echo "Options:"
            echo "  -h, --help    Show this help message"
            echo "  -f, --file    Analyze the specified file"
            PROCESSED=1
            shift
            exit 0
            ;;
            
        -f|--file )
            FILE=$2
            if ! [ -f "$FILE" ]; then
                echo "Error: File '$FILE' does not exist"
                exit 2
            fi
            
            echo "Analyzing file: $FILE"
            echo "===================="
            
            LINES=$(wc -l < "$FILE")
            WORDS=$(wc -w < "$FILE")
            CHARACTERS=$(wc -m < "$FILE")
            
            echo "Lines      : $LINES"
            echo "Words      : $WORDS"
            echo "Characters : $CHARACTERS"
            
            PROCESSED=1
            shift 2
            ;;
            
        * )
            echo "Error: Unknown option '$1'"
            echo "Try '$0 --help' for more information"
            exit 1
            ;;
    esac
done

# Kiểm tra nếu không có tham số
if [ $PROCESSED -eq 0 ]; then
    echo "Error: No options provided"
    echo "Try '$0 --help' for more information"
    exit 1
fi
```

## Phần 4: Tips và Best Practices

### 1. Kiểm Tra Tham Số
```bash
# Kiểm tra số lượng tham số
if [ "$#" -eq 0 ]; then
    echo "Error: No arguments provided"
    exit 1
fi

# Kiểm tra tham số file
if [ -z "$2" ]; then
    echo "Error: No file specified after -f"
    exit 1
fi
```

### 2. Error Handling
```bash
# Kiểm tra quyền đọc file
if [ ! -r "$FILE" ]; then
    echo "Error: Cannot read file '$FILE'"
    exit 2
fi

# Bắt lỗi khi đọc file
if ! LINES=$(wc -l < "$FILE" 2>/dev/null); then
    echo "Error: Failed to read file"
    exit 3
fi
```

### 3. Output Format
```bash
# Sử dụng format string
printf "%-12s: %d\n" "Lines" "$LINES"
printf "%-12s: %d\n" "Words" "$WORDS"
printf "%-12s: %d\n" "Characters" "$CHARACTERS"
```

### 4. Debug Mode
```bash
# Thêm option debug
-d|--debug )
    set -x  # Enable debug output
    shift
    ;;
```

