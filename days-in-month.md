# Hướng dẫn tính số ngày trong tháng (có tính năm nhuận)

## I. Phân tích yêu cầu

### 1. Input cần có:
- Tháng (1-12)
- Năm (số dương)

### 2. Output:
- Số ngày trong tháng
- Thông báo có phải năm nhuận không

## II. Các cách giải quyết

### 1. Cách 1: Script đơn giản
```bash
#!/bin/bash

# Tên file: days_in_month_simple.sh
# Mục đích: Tính số ngày trong tháng

# Nhập tháng và năm
echo "Nhập tháng (1-12): "
read month
echo "Nhập năm: "
read year

# Mảng số ngày trong các tháng
days=(31 28 31 30 31 30 31 31 30 31 30 31)

# Kiểm tra năm nhuận và điều chỉnh tháng 2
if [ $((year % 4)) -eq 0 ] && [ $((year % 100)) -ne 0 ] || [ $((year % 400)) -eq 0 ]; then
    days[1]=29
fi

# In kết quả
echo "Tháng $month năm $year có ${days[$((month-1))]} ngày"
```

**Giải thích:**
- Sử dụng mảng để lưu số ngày của các tháng
- Điều chỉnh tháng 2 nếu là năm nhuận
- Chưa có validate input

### 2. Cách 2: Script với validate input đầy đủ
```bash
#!/bin/bash

# Tên file: days_in_month_advanced.sh
# Mục đích: Tính số ngày trong tháng với validate input

# Hàm kiểm tra số nguyên dương
check_positive_integer() {
    local input=$1
    if [[ "$input" =~ ^[0-9]+$ ]]; then
        return 0
    else
        return 1
    fi
}

# Hàm kiểm tra năm nhuận
check_leap_year() {
    local year=$1
    if [ $((year % 4)) -eq 0 ] && [ $((year % 100)) -ne 0 ] || [ $((year % 400)) -eq 0 ]; then
        return 0
    else
        return 1
    fi
}

# Hàm tính số ngày trong tháng
get_days_in_month() {
    local month=$1
    local year=$2
    local days=(31 28 31 30 31 30 31 31 30 31 30 31)
    
    # Kiểm tra năm nhuận và điều chỉnh tháng 2
    if check_leap_year $year; then
        days[1]=29
    fi
    
    echo "${days[$((month-1))]}"
}

# Nhập và validate tháng
while true; do
    echo -n "Nhập tháng (1-12): "
    read month
    
    if ! check_positive_integer "$month"; then
        echo "Vui lòng nhập số nguyên dương!"
        continue
    fi
    
    if [ "$month" -lt 1 ] || [ "$month" -gt 12 ]; then
        echo "Tháng phải từ 1 đến 12!"
        continue
    fi
    
    break
done

# Nhập và validate năm
while true; do
    echo -n "Nhập năm: "
    read year
    
    if ! check_positive_integer "$year"; then
        echo "Vui lòng nhập số nguyên dương!"
        continue
    fi
    
    break
done

# Tính và hiển thị kết quả
days=$(get_days_in_month $month $year)

# In kết quả chi tiết
echo "Kết quả:"
echo "Tháng $month năm $year có $days ngày"
if check_leap_year $year; then
    echo "Năm $year là năm nhuận"
else
    echo "Năm $year không phải năm nhuận"
fi
```

### 3. Cách 3: Script với giao diện đẹp và nhiều tính năng
```bash
#!/bin/bash

# Tên file: days_in_month_full.sh
# Mục đích: Tính số ngày trong tháng với giao diện đẹp

# Định nghĩa màu sắc
GREEN='\033[0;32m'
RED='\033[0;31m'
BLUE='\033[0;34m'
NC='\033[0m'  # No Color

# Hàm kiểm tra số nguyên dương
check_positive_integer() {
    local input=$1
    if [[ "$input" =~ ^[0-9]+$ ]]; then
        return 0
    else
        return 1
    fi
}

# Hàm kiểm tra năm nhuận
check_leap_year() {
    local year=$1
    if [ $((year % 4)) -eq 0 ] && [ $((year % 100)) -ne 0 ] || [ $((year % 400)) -eq 0 ]; then
        return 0
    else
        return 1
    fi
}

# Hàm giải thích năm nhuận
explain_leap_year() {
    local year=$1
    echo -e "\n${BLUE}Giải thích về năm nhuận:${NC}"
    if check_leap_year $year; then
        echo -e "${GREEN}Năm $year là năm nhuận vì:${NC}"
        if [ $((year % 400)) -eq 0 ]; then
            echo "- Chia hết cho 400"
        else
            echo "- Chia hết cho 4 nhưng không chia hết cho 100"
        fi
    else
        echo -e "${RED}Năm $year không phải năm nhuận vì:${NC}"
        if [ $((year % 4)) -ne 0 ]; then
            echo "- Không chia hết cho 4"
        else
            echo "- Chia hết cho 100 nhưng không chia hết cho 400"
        fi
    fi
}

# Hàm in calendar tháng đó
print_month_calendar() {
    local month=$1
    local year=$2
    local days=$3
    
    echo -e "\n${BLUE}Lịch tháng $month năm $year:${NC}"
    echo "CN  T2  T3  T4  T5  T6  T7"
    
    # TODO: Thêm code in lịch chi tiết
}

# Main script
echo -e "${GREEN}=== Chương trình tính số ngày trong tháng ===${NC}"

# Nhập và validate tháng
while true; do
    echo -n "Nhập tháng (1-12): "
    read month
    
    if ! check_positive_integer "$month"; then
        echo -e "${RED}Vui lòng nhập số nguyên dương!${NC}"
        continue
    fi
    
    if [ "$month" -lt 1 ] || [ "$month" -gt 12 ]; then
        echo -e "${RED}Tháng phải từ 1 đến 12!${NC}"
        continue
    fi
    
    break
done

# Nhập và validate năm
while true; do
    echo -n "Nhập năm: "
    read year
    
    if ! check_positive_integer "$year"; then
        echo -e "${RED}Vui lòng nhập số nguyên dương!${NC}"
        continue
    fi
    
    break
done

# Tính số ngày
days=(31 28 31 30 31 30 31 31 30 31 30 31)
if check_leap_year $year; then
    days[1]=29
fi

# In kết quả
echo -e "\n${GREEN}=== Kết quả ===${NC}"
echo "Tháng $month năm $year có ${days[$((month-1))]} ngày"

# Giải thích năm nhuận
explain_leap_year $year

# In calendar
print_month_calendar $month $year ${days[$((month-1))]}
```

## III. Giải thích chi tiết về năm nhuận

### 1. Quy tắc xác định năm nhuận:
- Chia hết cho 4 VÀ không chia hết cho 100
- HOẶC chia hết cho 400

### 2. Code kiểm tra:
```bash
if [ $((year % 4)) -eq 0 ] && [ $((year % 100)) -ne 0 ] || [ $((year % 400)) -eq 0 ]
```

**Giải thích:**
- `year % 4`: Kiểm tra chia hết cho 4
- `year % 100`: Kiểm tra chia hết cho 100
- `year % 400`: Kiểm tra chia hết cho 400

## IV. Hướng dẫn sử dụng

### 1. Tạo và chạy script
```bash
# Tạo file script
vi days_in_month.sh

# Phân quyền thực thi
chmod +x days_in_month.sh

# Chạy script
./days_in_month.sh
```

### 2. Test cases
```bash
# Test năm nhuận
2000 -> 29 days (tháng 2)
2100 -> 28 days (tháng 2)
2020 -> 29 days (tháng 2)

# Test các tháng khác
7 2023 -> 31 days
4 2023 -> 30 days
```

## V. Best Practices

### 1. Input Validation
- Kiểm tra số nguyên dương
- Kiểm tra tháng hợp lệ (1-12)
- Xử lý input không hợp lệ

### 2. Code Organization
- Tách thành các functions
- Comment đầy đủ
- Đặt tên biến rõ ràng

### 3. User Interface
- Thông báo rõ ràng
- Hiển thị màu sắc
- Giải thích chi tiết

---

*Note: Script này dành cho mục đích học tập. Trong thực tế có thể cần thêm các xử lý khác tùy yêu cầu cụ thể.*