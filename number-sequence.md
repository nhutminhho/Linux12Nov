# Hướng dẫn giải bài tập in dãy số tăng giảm

## I. Phân tích yêu cầu

### 1. Input
- Nhập một số nguyên dương n
- Cần validate input hợp lệ

### 2. Output
- Dãy số từ 0 đến n (tăng dần)
- Dãy số từ n về 0 (giảm dần)

## II. Các cách giải quyết

### 1. Cách 1: Script đơn giản nhất
```bash
#!/bin/bash

# Tên file: print_numbers_simple.sh
# Mục đích: In dãy số theo thứ tự tăng và giảm

# Nhập số n
echo "Nhập số n: "
read n

# In dãy số tăng dần
echo "Dãy số tăng dần:"
for ((i=0; i<=n; i++))
do
    echo -n "$i "
done
echo # Xuống dòng

# In dãy số giảm dần
echo "Dãy số giảm dần:"
for ((i=n; i>=0; i--))
do
    echo -n "$i "
done
echo # Xuống dòng
```

**Giải thích:**
- Script đơn giản không có validate input
- Sử dụng vòng lặp for với C-style syntax
- echo -n để in trên cùng một dòng

### 2. Cách 2: Script với validate input
```bash
#!/bin/bash

# Tên file: print_numbers_validate.sh
# Mục đích: In dãy số với kiểm tra input

# Hàm kiểm tra số nguyên dương
kiem_tra_so() {
    # $1 là tham số truyền vào hàm
    if [[ "$1" =~ ^[0-9]+$ ]]; then
        return 0 # True - là số nguyên dương
    else
        return 1 # False - không phải số nguyên dương
    fi
}

# Hàm in dãy số
in_day_so() {
    local n=$1
    local mode=$2
    
    case $mode in
        "tang")
            for ((i=0; i<=n; i++)); do
                echo -n "$i "
            done
            ;;
        "giam")
            for ((i=n; i>=0; i--)); do
                echo -n "$i "
            done
            ;;
    esac
    echo # Xuống dòng
}

# Nhập và kiểm tra số n
while true; do
    echo -n "Nhập số n (số nguyên dương): "
    read n
    
    if kiem_tra_so "$n"; then
        break
    else
        echo "Vui lòng nhập số nguyên dương!"
    fi
done

# In kết quả
echo "Dãy số tăng dần:"
in_day_so "$n" "tang"

echo "Dãy số giảm dần:"
in_day_so "$n" "giam"
```

**Giải thích chi tiết:**

1. **Hàm kiem_tra_so:**
   - Sử dụng regex ^[0-9]+$ để kiểm tra số nguyên dương
   - ^ : bắt đầu chuỗi
   - [0-9] : chỉ chấp nhận các chữ số
   - + : một hoặc nhiều chữ số
   - $ : kết thúc chuỗi

2. **Hàm in_day_so:**
   - Nhận hai tham số: số n và mode (tăng/giảm)
   - Sử dụng case để xử lý hai mode khác nhau
   - echo -n để in trên cùng một dòng

### 3. Cách 3: Script với định dạng output đẹp
```bash
#!/bin/bash

# Tên file: print_numbers_formatted.sh
# Mục đích: In dãy số với định dạng đẹp

# Colors
GREEN='\033[0;32m'
NC='\033[0m'  # No Color

# Hàm kiểm tra input
kiem_tra_input() {
    local input=$1
    
    # Kiểm tra rỗng
    if [ -z "$input" ]; then
        echo "Input không được để trống!"
        return 1
    fi
    
    # Kiểm tra số nguyên dương
    if ! [[ "$input" =~ ^[0-9]+$ ]]; then
        echo "Vui lòng nhập số nguyên dương!"
        return 1
    fi
    
    # Kiểm tra giới hạn
    if [ "$input" -gt 100 ]; then
        echo "Số quá lớn! Vui lòng nhập số nhỏ hơn hoặc bằng 100"
        return 1
    fi
    
    return 0
}

# Hàm in dãy số với định dạng
in_day_so_formatted() {
    local n=$1
    local mode=$2
    local count=0
    
    # In header
    echo -e "${GREEN}=== Dãy số $mode ===${NC}"
    
    # In số theo mode
    if [ "$mode" = "tăng dần" ]; then
        for ((i=0; i<=n; i++)); do
            printf "%3d " $i
            ((count++))
            # Xuống dòng sau mỗi 10 số
            [ $((count % 10)) -eq 0 ] && echo
        done
    else
        for ((i=n; i>=0; i--)); do
            printf "%3d " $i
            ((count++))
            # Xuống dòng sau mỗi 10 số
            [ $((count % 10)) -eq 0 ] && echo
        done
    fi
    
    # Xuống dòng nếu chưa xuống
    [ $((count % 10)) -ne 0 ] && echo
    echo
}

# Main script
echo "Chương trình in dãy số"
echo "---------------------"

# Nhập và validate số n
while true; do
    echo -n "Nhập số n: "
    read n
    
    if kiem_tra_input "$n"; then
        break
    fi
done

# In kết quả
in_day_so_formatted "$n" "tăng dần"
in_day_so_formatted "$n" "giảm dần"
```

## III. Hướng dẫn sử dụng

### 1. Tạo và chạy script
```bash
# Tạo file script
vi print_numbers.sh

# Phân quyền thực thi
chmod +x print_numbers.sh

# Chạy script
./print_numbers.sh
```

### 2. Test cases
```bash
# Test input hợp lệ
echo "5" | ./print_numbers.sh

# Test input không hợp lệ
echo "-1" | ./print_numbers.sh
echo "abc" | ./print_numbers.sh
echo "" | ./print_numbers.sh
```

## IV. Xử lý các trường hợp đặc biệt

### 1. Input là 0
```bash
# Nếu n = 0, chỉ in ra số 0
if [ "$n" -eq 0 ]; then
    echo "0"
    exit 0
fi
```

### 2. Input quá lớn
```bash
# Giới hạn input
if [ "$n" -gt 1000 ]; then
    echo "Số quá lớn!"
    exit 1
fi
```

## V. Best Practices

### 1. Input Validation
- Kiểm tra input rỗng
- Kiểm tra định dạng số
- Kiểm tra giới hạn

### 2. Output Format
- Căn lề số
- Ngắt dòng hợp lý
- Thêm màu sắc và định dạng

### 3. Code Organization
- Tách thành các hàm riêng
- Comment đầy đủ
- Xử lý lỗi rõ ràng

## VI. Lưu ý khi thực hiện

1. **Performance:**
   - Với số lớn, nên xem xét độ phức tạp
   - Tối ưu bộ nhớ sử dụng

2. **User Experience:**
   - Thông báo lỗi rõ ràng
   - Output dễ đọc
   - Hướng dẫn sử dụng

3. **Maintenance:**
   - Code dễ đọc
   - Dễ mở rộng
   - Dễ debug

---

*Note: Scripts trên dành cho mục đích học tập. Trong thực tế có thể cần thêm các xử lý khác tùy vào yêu cầu cụ thể.*