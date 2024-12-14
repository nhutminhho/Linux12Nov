# Hướng Dẫn Thực Hành Shell Bash Trên AlmaLinux

## Phần 1: Giới Thiệu về Bash Shell

### 1.1. Shell là gì?
Shell là một trình thông dịch lệnh, đóng vai trò là giao diện giữa người dùng và kernel. Shell có nhiệm vụ:
- Nhận lệnh từ người dùng
- Thông dịch các lệnh
- Chuyển cho kernel xử lý

### 1.2. Đặc điểm của Bash Shell
- Là shell mặc định trên AlmaLinux
- Được nhận biết bằng ký tự `$` với người dùng thường và `#` với root
- Được lưu trữ tại `/bin/bash`

## Phần 2: Thực Hành Với Biến (Variables)

### Bài 2.1: Làm việc với biến cục bộ
```bash
# Bước 1: Tạo biến cục bộ
TEN="AlmaLinux"
PHIENBAN=9

# Bước 2: Hiển thị giá trị biến
echo $TEN
echo $PHIENBAN

# Bước 3: Tạo biến có chứa khoảng trắng
MOTA="Hệ điều hành Linux doanh nghiệp"
echo $MOTA

# Kết quả mong đợi:
# AlmaLinux
# 9
# Hệ điều hành Linux doanh nghiệp
```

### Bài 2.2: Làm việc với biến môi trường
```bash
# Bước 1: Xem các biến môi trường hiện có
echo $HOME
echo $PWD
echo $USER

# Bước 2: Tạo biến môi trường mới
export TRUONG="Cao Dang ABC"
echo $TRUONG

# Bước 3: Kiểm tra biến trong shell con
bash
echo $TRUONG

# Bước 4: Hủy biến
unset TRUONG
echo $TRUONG    # Sẽ không hiển thị gì
```

## Phần 3: Thực Hành Chuyển Hướng Input/Output

### Bài 3.1: Chuyển hướng đầu ra
```bash
# Bước 1: Tạo file và ghi nội dung
echo "Dong 1" > test.txt
echo "Dong 2" >> test.txt
cat test.txt

# Bước 2: Lưu kết quả lệnh ls
ls -l /home > danhsach.txt
cat danhsach.txt

# Bước 3: Chuyển hướng cả output và error
ls /khongtontai &> ketqua.txt
cat ketqua.txt
```

### Bài 3.2: Chuyển hướng đầu vào
```bash
# Bước 1: Tạo file dữ liệu
echo "AlmaLinux
CentOS
Ubuntu" > os.txt

# Bước 2: Sử dụng chuyển hướng đầu vào
sort < os.txt

# Bước 3: Đếm số dòng
wc -l < os.txt
```

## Phần 4: Thực Hành Với Lịch Sử Lệnh

### Bài 4.1: Xem và sử dụng lịch sử
```bash
# Bước 1: Xem file lưu lịch sử
echo $HISTFILE

# Bước 2: Xem kích thước lịch sử
echo $HISTSIZE

# Bước 3: Xem lịch sử lệnh
history
history | tail -n 5   # Xem 5 lệnh cuối

# Bước 4: Thực thi lệnh từ lịch sử
!-1    # Thực thi lệnh cuối cùng
!50    # Thực thi lệnh số 50
```

## Phần 5: Thực Hành Tab Completion

### Bài 5.1: Hoàn thành lệnh và đường dẫn
```bash
# Thử các lệnh sau và nhấn TAB:
cd /et[TAB]
ls /var/lo[TAB]
sys[TAB][TAB]    # Nhấn TAB hai lần để xem tất cả các lựa chọn
```

## Phần 6: Thực Hành Tilde Expansion

### Bài 6.1: Sử dụng ký tự ~
```bash
# Bước 1: Di chuyển đến thư mục home
cd ~
pwd

# Bước 2: Xem nội dung thư mục home
ls ~

# Bước 3: Thử với thư mục người dùng khác
echo ~root    # Hiển thị home của root
```

## Bài Tập Thực Hành Tổng Hợp

### Bài Tập 1: Quản lý file và thư mục
```bash
# Tạo cấu trúc thư mục sau:
mkdir -p ~/lab/bai1/{data,backup,logs}
cd ~/lab/bai1

# Tạo các file test
echo "File 1" > data/file1.txt
echo "File 2" > data/file2.txt

# Sao chép file
cp data/*.txt backup/

# Kiểm tra kết quả
ls -R
```

### Bài Tập 2: Làm việc với biến và chuyển hướng
```bash
# Tạo script đơn giản
echo '#!/bin/bash
echo "Thông tin hệ thống:"
echo "Người dùng: $USER"
echo "Thư mục hiện tại: $PWD"
echo "Shell: $SHELL"' > system_info.sh

# Cấp quyền thực thi
chmod +x system_info.sh

# Chạy và lưu kết quả
./system_info.sh > logs/system_info.log

# Xem kết quả
cat logs/system_info.log
```

### Bài Tập 3: Tìm kiếm và lọc dữ liệu
```bash
# Tạo file dữ liệu
echo "apple,red,fruit
banana,yellow,fruit
carrot,orange,vegetable
tomato,red,vegetable" > data/foods.txt

# Tìm các dòng có chứa 'red'
grep 'red' data/foods.txt > logs/red_foods.log

# Đếm số dòng kết quả
wc -l logs/red_foods.log
```

## Ghi Chú Quan Trọng

### Các lỗi thường gặp và cách khắc phục

1. **Permission denied**
   ```bash
   # Kiểm tra quyền
   ls -l tên_file
   
   # Thêm quyền thực thi
   chmod +x tên_file
   ```

2. **Command not found**
   ```bash
   # Kiểm tra đường dẫn
   echo $PATH
   
   # Kiểm tra vị trí lệnh
   which tên_lệnh
   ```

3. **No such file or directory**
   ```bash
   # Kiểm tra thư mục hiện tại
   pwd
   
   # Liệt kê các file/thư mục
   ls -la
   ```

### Mẹo thực hành

1. Luôn sao lưu dữ liệu trước khi thực hiện các lệnh nguy hiểm
2. Sử dụng `man` hoặc `--help` để xem hướng dẫn chi tiết
3. Tạo alias cho các lệnh thường dùng
4. Ghi chép lại các lệnh đã thực hiện

### Bài tập nâng cao

1. Viết script tự động sao lưu thư mục
2. Tạo alias cho các tổ hợp lệnh phức tạp
3. Thực hành với các biểu thức chính quy (regex)
4. Tạo menu tương tác trong script

## Kết luận

Sau khi hoàn thành các bài thực hành, sinh viên sẽ:
- Hiểu và sử dụng thành thạo các lệnh cơ bản
- Biết cách quản lý file và thư mục
- Nắm vững cách làm việc với biến
- Thực hiện được các tác vụ chuyển hướng input/output
- Sử dụng được các tính năng hỗ trợ như tab completion và tilde expansion

