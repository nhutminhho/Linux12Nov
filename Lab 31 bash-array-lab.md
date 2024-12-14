# Lab Thực Hành: Arrays Trong Bash Script

## Phần 1: Giải Thích Script Cơ Bản

### 1.1 Cấu Trúc Script
```bash
#!/bin/bash
# Giải thích:
# - Dòng đầu tiên chỉ định shell sẽ chạy script
# - /bin/bash là đường dẫn đến bash shell
```

### 1.2 Khởi Tạo Mảng
```bash
ARRAY=($(ls *.sh))
# Giải thích:
# - ARRAY là tên mảng
# - $(ls *.sh) thực hiện lệnh ls và lấy kết quả
# - *.sh tìm tất cả file có đuôi .sh
# - () khai báo mảng
```

### 1.3 Khởi Tạo Biến Đếm
```bash
COUNT=0
# Giải thích:
# - COUNT dùng để đếm số lượng file
# - Bắt đầu từ 0 vì index mảng bắt đầu từ 0
```

## Phần 2: Thực Hành Cơ Bản

### Bài 1: Tạo Files Test
```bash
# Tạo một số file .sh để test
touch test1.sh test2.sh test3.sh

# Cấp quyền thực thi cho một số file
chmod +x test1.sh test3.sh

# Giải thích:
# - touch tạo file mới
# - chmod +x cấp quyền thực thi
```

### Bài 2: Script Hoàn Chỉnh
```bash
#!/bin/bash

# Khai báo mảng chứa tất cả file .sh
ARRAY=($(ls *.sh))

# Khởi tạo biến đếm
COUNT=0

# In tiêu đề
echo -e "FILE NAME \t WRITEABLE"

# Duyệt qua từng phần tử trong mảng
for FILE in "${ARRAY[@]}"; do
    # In tên file
    echo -n $FILE
    
    # In độ dài tên file
    echo -n "[${#ARRAY[$COUNT]}]"
    
    # Kiểm tra quyền thực thi
    if [ -x "$FILE" ]; then
        echo -e "\t YES"
    else
        echo -e "\t NO"
    fi
    
    # Tăng biến đếm
    let COUNT++
done

# Giải thích từng phần:
# - "${ARRAY[@]}" : lấy tất cả phần tử trong mảng
# - echo -n : in không xuống dòng
# - ${#ARRAY[$COUNT]} : lấy độ dài của phần tử
# - [ -x "$FILE" ] : kiểm tra quyền thực thi
# - let COUNT++ : tăng biến đếm
```

## Phần 3: Thực Hành Nâng Cao

### Bài 1: Thêm Tính Năng
```bash
#!/bin/bash

# Khai báo mảng
ARRAY=($(ls *.sh))
COUNT=0

# Thêm đếm tổng số file
TOTAL=${#ARRAY[@]}
echo "Tổng số file .sh: $TOTAL"

# In tiêu đề
echo -e "\nFILE NAME \t WRITEABLE \t SIZE"

# Duyệt mảng
for FILE in "${ARRAY[@]}"; do
    # In tên file
    echo -n $FILE
    
    # In độ dài tên
    echo -n "[${#ARRAY[$COUNT]}]"
    
    # Kiểm tra quyền thực thi
    if [ -x "$FILE" ]; then
        echo -n -e "\t YES"
    else
        echo -n -e "\t NO"
    fi
    
    # Thêm kích thước file
    SIZE=$(stat -f %z "$FILE")
    echo -e "\t\t $SIZE bytes"
    
    let COUNT++
done

# Giải thích thêm:
# - ${#ARRAY[@]} : lấy độ dài mảng
# - stat -f %z : lấy kích thước file
```

## Phần 4: Tips và Lưu Ý

### 1. Các Toán Tử Mảng Quan Trọng
```bash
# Lấy tất cả phần tử
${ARRAY[@]}

# Lấy số lượng phần tử
${#ARRAY[@]}

# Lấy phần tử theo index
${ARRAY[0]}

# Lấy độ dài phần tử
${#ARRAY[0]}
```

### 2. Debug Script
```bash
# Thêm vào đầu script để debug
set -x

# Hoặc chạy với bash -x
bash -x script.sh
```

### 3. Best Practices
1. **Kiểm Tra Dữ Liệu:**
   ```bash
   # Kiểm tra mảng có rỗng không
   if [ ${#ARRAY[@]} -eq 0 ]; then
       echo "Không tìm thấy file .sh"
       exit 1
   fi
   ```

2. **Xử Lý Lỗi:**
   ```bash
   # Kiểm tra quyền đọc file
   if [ ! -r "$FILE" ]; then
       echo "Không thể đọc file $FILE"
       continue
   fi
   ```

3. **Comment Code:**
   ```bash
   # Luôn thêm comment giải thích
   # Giải thích các biến quan trọng
   # Ghi chú các trường hợp đặc biệt
   ```

