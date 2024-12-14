# Bài tập tính toán với hai số

## I. Phân tích yêu cầu

### 1. Input
- Nhập vào hai số từ bàn phím
- Cần kiểm tra tính hợp lệ của input

### 2. Output
- Tổng hai số
- Hiệu hai số
- Tích hai số
- Thương hai số (chú ý chia cho 0)

## II. Giải pháp

### 1. Cách 1: Script đơn giản nhất
```bash
#!/bin/bash

# Tên file: calc_simple.sh
# Mục đích: Tính toán đơn giản với 2 số

# Nhập số thứ nhất
echo "Nhập số thứ nhất: "
read num1

# Nhập số thứ hai
echo "Nhập số thứ hai: "
read num2

# Thực hiện tính toán
echo "Tổng: $((num1 + num2))"
echo "Hiệu: $((num1 - num2))"
echo "Tích: $((num1 * num2))"

# Kiểm tra chia cho 0
if [ "$num2" -eq 0 ]; then
    echo "Không thể chia cho 0!"
else
    echo "Thương: $((num1 / num2))"
fi
```

**Giải thích:**
- Script này đơn giản nhưng chỉ hoạt động với số nguyên
- Không kiểm tra input có phải là số không
- Sử dụng phép tính số học trong shell (())

### 2. Cách 2: Script với kiểm tra input và số thực
```bash
#!/bin/bash

# Tên file: calc_advanced.sh
# Mục đích: Tính toán với 2 số và xử lý số thực

# Hàm kiểm tra số hợp lệ
kiem_tra_so() {
    local input=$1
    # Kiểm tra input có phải là số không (bao gồm số thực)
    if [[ $input =~ ^[+-]?[0-9]*\.?[0-9]+$ ]]; then
        return 0 # True
    else
        return 1 # False
    fi
}

# Hàm tính toán với bc
tinh_toan() {
    local num1=$1
    local num2=$2
    
    # In kết quả với 2 số thập phân
    echo "Tổng: $(echo "scale=2; $num1 + $num2" | bc)"
    echo "Hiệu: $(echo "scale=2; $num1 - $num2" | bc)"
    echo "Tích: $(echo "scale=2; $num1 * $num2" | bc)"
    
    # Kiểm tra chia cho 0
    if (( $(echo "$num2 == 0" | bc -l) )); then
        echo "Không thể chia cho 0!"
    else
        echo "Thương: $(echo "scale=2; $num1 / $num2" | bc)"
    fi
}

# Nhập và kiểm tra số thứ nhất
while true; do
    echo -n "Nhập số thứ nhất: "
    read num1
    if kiem_tra_so "$num1"; then
        break
    else
        echo "Vui lòng nhập số hợp lệ!"
    fi
done

# Nhập và kiểm tra số thứ hai
while true; do
    echo -n "Nhập số thứ hai: "
    read num2
    if kiem_tra_so "$num2"; then
        break
    else
        echo "Vui lòng nhập số hợp lệ!"
    fi
done

# Thực hiện tính toán
tinh_toan "$num1" "$num2"
```

**Giải thích chi tiết:**

1. **Hàm kiem_tra_so:**
   - Sử dụng regex để kiểm tra số hợp lệ
   - Chấp nhận số nguyên và số thực
   - Chấp nhận số âm và số dương

2. **Hàm tinh_toan:**
   - Sử dụng `bc` để tính toán số thực
   - `scale=2`: giới hạn 2 số sau dấu phẩy
   - Kiểm tra chia cho 0 trước khi thực hiện phép chia

3. **Vòng lặp nhập liệu:**
   - Lặp cho đến khi nhập đúng định dạng số
   - Thông báo lỗi nếu nhập sai
   - Tách biệt logic nhập và tính toán

### 3. Cách 3: Script với GUI đơn giản (sử dụng dialog)
```bash
#!/bin/bash

# Tên file: calc_gui.sh
# Mục đích: Tính toán với giao diện đơn giản

# Kiểm tra dialog đã được cài đặt chưa
if ! command -v dialog &> /dev/null; then
    echo "Cần cài đặt dialog: sudo apt-get install dialog"
    exit 1
fi

# Nhập số thứ nhất
num1=$(dialog --stdout --inputbox "Nhập số thứ nhất:" 0 0)
if [ $? -ne 0 ]; then
    clear
    exit 1
fi

# Nhập số thứ hai
num2=$(dialog --stdout --inputbox "Nhập số thứ hai:" 0 0)
if [ $? -ne 0 ]; then
    clear
    exit 1
fi

# Tính toán và hiển thị kết quả
result=$(echo -e "Tổng: $(echo "$num1 + $num2" | bc)\n")
result+=$(echo -e "Hiệu: $(echo "$num1 - $num2" | bc)\n")
result+=$(echo -e "Tích: $(echo "$num1 * $num2" | bc)\n")

if [ $(echo "$num2 == 0" | bc) -eq 1 ]; then
    result+="Không thể chia cho 0!"
else
    result+=$(echo -e "Thương: $(echo "scale=2; $num1 / $num2" | bc)")
fi

# Hiển thị kết quả
dialog --title "Kết quả" --msgbox "$result" 0 0
clear
```

## III. Hướng dẫn sử dụng

### 1. Tạo và chạy script
```bash
# Tạo file script
vi calc_advanced.sh

# Phân quyền thực thi
chmod +x calc_advanced.sh

# Chạy script
./calc_advanced.sh
```

### 2. Testing
```bash
# Test với các loại input
# Số nguyên
echo "5 3" | ./calc_advanced.sh

# Số thực
echo "3.14 2.5" | ./calc_advanced.sh

# Số âm
echo "-5 -3" | ./calc_advanced.sh

# Chia cho 0
echo "5 0" | ./calc_advanced.sh
```

## IV. Xử lý các trường hợp đặc biệt

### 1. Input không hợp lệ
```bash
# Kiểm tra input không phải số
if ! [[ $input =~ ^[+-]?[0-9]*\.?[0-9]+$ ]]; then
    echo "Input không hợp lệ"
    continue
fi
```

### 2. Chia cho 0
```bash
# Kiểm tra trước khi chia
if (( $(echo "$num2 == 0" | bc -l) )); then
    echo "Không thể chia cho 0"
else
    # Thực hiện phép chia
    echo "scale=2; $num1 / $num2" | bc
fi
```

### 3. Số quá lớn hoặc quá nhỏ
```bash
# Kiểm tra giới hạn của số
if (( $(echo "$num1 > 999999999" | bc -l) )); then
    echo "Số quá lớn!"
    continue
fi
```

## V. Best Practices

### 1. Validate Input
- Kiểm tra định dạng số
- Kiểm tra giới hạn
- Xử lý input không hợp lệ

### 2. Error Handling
- Thông báo lỗi rõ ràng
- Cho phép nhập lại khi sai
- Thoát chương trình an toàn

### 3. Output Format
- Định dạng số thập phân
- Căn lề kết quả
- Thông báo rõ ràng

---

*Note: Scripts trên dành cho mục đích học tập. Trong thực tế có thể cần thêm các xử lý khác tùy vào yêu cầu cụ thể.*