# Lab Thực Hành: Bài Tập Bash Script

## Bài 1: Tạo Users Marketing

```bash
#!/bin/bash

# Tạo group marketing nếu chưa có
sudo groupadd marketing

# Tạo 50 users
for i in {1..50}; do
    username="mar-user$i"
    
    # Tạo user với home directory
    sudo useradd -m \
        -g marketing \
        -s /bin/bash \
        -c "Marketing User $i" \
        "$username"
    
    # Đặt mật khẩu (có thể thay đổi theo yêu cầu)
    echo "${username}:Pass123" | sudo chpasswd
    
    echo "Created user: $username"
done

# Giải thích:
# - for {1..50}: tạo vòng lặp từ 1 đến 50
# - -m: tạo home directory
# - -g marketing: thêm vào group marketing
# - -s /bin/bash: sử dụng bash shell
# - -c: thêm comment cho user
```

## Bài 2: Kiểm Tra Files trong /etc

```bash
#!/bin/bash

# Liệt kê các file (không phải thư mục) trong /etc
find /etc -maxdepth 1 -type f -printf "%f\n"

# Giải thích:
# - find: lệnh tìm kiếm
# - -maxdepth 1: chỉ tìm ở level hiện tại
# - -type f: chỉ tìm file
# - -printf "%f\n": chỉ in tên file
```

## Bài 3: Tính Toán Hai Số

```bash
#!/bin/bash

# Nhập số thứ nhất
read -p "Nhập số thứ nhất: " num1

# Nhập số thứ hai
read -p "Nhập số thứ hai: " num2

# Kiểm tra input có phải là số không
if ! [[ "$num1" =~ ^[0-9]+$ ]] || ! [[ "$num2" =~ ^[0-9]+$ ]]; then
    echo "Vui lòng nhập số!"
    exit 1
fi

# Tính toán
echo "Tổng: $((num1 + num2))"
echo "Hiệu: $((num1 - num2))"
echo "Tích: $((num1 * num2))"

# Kiểm tra chia cho 0
if [ "$num2" -eq 0 ]; then
    echo "Không thể chia cho 0!"
else
    echo "Thương: $((num1 / num2))"
fi

# Giải thích:
# - read -p: hiển thị prompt và nhập giá trị
# - =~ ^[0-9]+$: kiểm tra có phải số không
# - $(( )): thực hiện phép tính
```

## Bài 4: In Dãy Số

```bash
#!/bin/bash

# Nhập số n
read -p "Nhập số n: " n

# Kiểm tra input
if ! [[ "$n" =~ ^[0-9]+$ ]]; then
    echo "Vui lòng nhập số nguyên dương!"
    exit 1
fi

# In dãy tăng dần
echo "Dãy tăng dần:"
for ((i=0; i<=n; i++)); do
    echo -n "$i "
done
echo

# In dãy giảm dần
echo "Dãy giảm dần:"
for ((i=n; i>=0; i--)); do
    echo -n "$i "
done
echo

# Giải thích:
# - for ((i=0; i<=n; i++)): vòng lặp C-style
# - echo -n: in không xuống dòng
```

## Bài 5: Kiểm Tra File Text

```bash
#!/bin/bash

# Nhập tên file
read -p "Nhập tên file: " filename

# Kiểm tra file tồn tại
if [ ! -f "$filename" ]; then
    echo "File không tồn tại!"
    exit 1
fi

# Đếm số dòng
lines=$(wc -l < "$filename")
echo "Số dòng: $lines"

# In dòng cuối
echo "Dòng cuối:"
tail -n 1 "$filename"

# Giải thích:
# - wc -l: đếm số dòng
# - tail -n 1: lấy dòng cuối cùng
```

## Bài 6: Tính Số Ngày Trong Tháng

```bash
#!/bin/bash

# Nhập tháng và năm
read -p "Nhập tháng (1-12): " month
read -p "Nhập năm: " year

# Kiểm tra input
if ! [[ "$month" =~ ^[1-9]|1[0-2]$ ]] || ! [[ "$year" =~ ^[0-9]{4}$ ]]; then
    echo "Input không hợp lệ!"
    exit 1
fi

# Kiểm tra năm nhuận
is_leap=0
if (( year % 4 == 0 )) && (( year % 100 != 0 )) || (( year % 400 == 0 )); then
    is_leap=1
fi

# Xác định số ngày
case $month in
    2)
        if [ $is_leap -eq 1 ]; then
            days=29
        else
            days=28
        fi
        ;;
    4|6|9|11)
        days=30
        ;;
    *)
        days=31
        ;;
esac

echo "Tháng $month năm $year có $days ngày"

# Giải thích:
# - case: kiểm tra nhiều điều kiện
# - Năm nhuận: chia hết cho 4 và không chia hết cho 100, hoặc chia hết cho 400
```

## Tips và Lưu Ý

### 1. Input Validation
```bash
# Luôn kiểm tra input
if ! [[ $input =~ ^[0-9]+$ ]]; then
    echo "Input không hợp lệ!"
    exit 1
fi
```

### 2. Error Handling
```bash
# Kiểm tra lỗi và thông báo
if [ $? -ne 0 ]; then
    echo "Có lỗi xảy ra!"
    exit 1
fi
```

### 3. Cách Test
```bash
# Test với các trường hợp:
- Input hợp lệ
- Input không hợp lệ
- Giá trị biên
- Trường hợp đặc biệt (ví dụ: chia cho 0)
```

### 4. Best Practices
1. **Comment Code:**
   ```bash
   # Giải thích mục đích
   # Mô tả input/output
   # Ghi chú các trường hợp đặc biệt
   ```

2. **Modular Code:**
   ```bash
   # Tách thành functions
   function validate_input() {
       # Code kiểm tra
   }
   ```

3. **Debug Mode:**
   ```bash
   # Thêm option debug
   set -x # Bật debug
   set +x # Tắt debug
   ```

