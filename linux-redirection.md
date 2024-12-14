# Toán tử chuyển hướng (redirection) trong Linux

## I. Giới thiệu về File Descriptors

File descriptors là các số nguyên được sử dụng để định danh các luồng I/O:
- `0`: stdin (Standard Input)
- `1`: stdout (Standard Output)
- `2`: stderr (Standard Error)

## II. Chi tiết các toán tử

### 1. `>` (Standard Output Redirection)
```bash
# Chuyển hướng stdout tới file
echo "hello" > output.txt

# Ghi đè nội dung file nếu đã tồn tại
ls > files.txt
```

**Giải thích:**
- Chỉ chuyển hướng stdout (file descriptor 1)
- Sẽ ghi đè file nếu đã tồn tại
- Tạo file mới nếu chưa tồn tại

### 2. `>>` (Append Output)
```bash
# Thêm vào cuối file
echo "line 1" >> log.txt
echo "line 2" >> log.txt

# Log commands output
date >> system.log
uptime >> system.log
```

**Giải thích:**
- Thêm stdout vào cuối file
- Không ghi đè nội dung cũ
- Tạo file mới nếu chưa tồn tại

### 3. `2>` (Standard Error Redirection)
```bash
# Chuyển hướng stderr tới file
ls nonexistent 2> errors.txt

# Lọc lỗi khi chạy script
./script.sh 2> script_errors.log
```

**Giải thích:**
- Chỉ chuyển hướng stderr (file descriptor 2)
- Hữu ích khi cần log lỗi riêng
- Thường dùng trong scripts

### 4. `&>` (Combined Output Redirection)
```bash
# Chuyển hướng cả stdout và stderr
command &> all_output.txt

# Tương đương với
command > all_output.txt 2>&1
```

**Giải thích:**
- Chuyển hướng cả stdout và stderr
- Syntax ngắn gọn hơn `> file 2>&1`
- Thường dùng khi cần log tất cả output

### 5. `/dev/null` (Null Device)
```bash
# Bỏ qua stdout
command > /dev/null

# Bỏ qua stderr
command 2> /dev/null

# Bỏ qua tất cả output
command &> /dev/null
```

**Giải thích:**
- `/dev/null` là thiết bị đặc biệt
- Mọi data ghi vào đây sẽ bị hủy
- Thường dùng để "im lặng" commands

### 6. `|` (Pipe)
```bash
# Chuyển output của command1 thành input của command2
command1 | command2

# Ví dụ thực tế
ls -l | grep ".txt"
ps aux | grep "nginx"
```

**Giải thích:**
- Kết nối stdout của command1 với stdin của command2
- Cho phép xử lý dữ liệu theo pipeline
- Rất powerful trong shell scripting

### 7. `<` (Input Redirection)
```bash
# Đọc input từ file
sort < unsorted.txt

# Multiple input sources
cat < file1.txt < file2.txt
```

**Giải thích:**
- Lấy input từ file thay vì keyboard
- File phải tồn tại trước
- Có thể kết hợp với output redirection

### 8. `<<` (Here Document)
```bash
# Nhập nhiều dòng text
cat << EOF
Line 1
Line 2
Line 3
EOF

# Tạo file với nhiều dòng
cat << EOF > config.txt
server {
    listen 80;
    server_name example.com;
}
EOF
```

**Giải thích:**
- Cho phép nhập text nhiều dòng
- EOF là delimiter (có thể thay đổi)
- Hữu ích khi tạo files/configs

## III. Use Cases và Best Practices

### 1. Logging
```bash
# Log với timestamp
echo "$(date): Command executed" >> app.log

# Log errors riêng
./script.sh 2>> errors.log 1>> output.log
```

### 2. Debugging
```bash
# Debug mode với stderr
bash -x script.sh 2> debug.log

# Capture tất cả output
script.sh &> full_debug.log
```

### 3. Silent Mode
```bash
# Chạy lệnh không hiển thị output
command > /dev/null 2>&1

# Chỉ hiển thị lỗi
command > /dev/null
```

### 4. File Processing
```bash
# Sort và save
sort < input.txt > sorted.txt

# Process multiple files
cat file1 file2 | sort | uniq > unique_lines.txt
```

## IV. Best Practices

### 1. Error Handling
```bash
# Log errors và output riêng
command > output.log 2> error.log

# Kiểm tra lỗi trong scripts
if ! command > output.log 2> error.log; then
    echo "Command failed"
    exit 1
fi
```

### 2. File Management
```bash
# Backup trước khi ghi đè
cp file.txt file.txt.bak
command > file.txt

# Append thay vì overwrite cho logging
echo "Log entry" >> app.log
```

### 3. Performance
```bash
# Tránh pipe khi không cần thiết
command > output.txt  # Tốt hơn
cat output.txt | grep "pattern"  # Không cần thiết

# Sử dụng /dev/null cho unwanted output
command &> /dev/null &  # Background process
```

## V. Common Pitfalls

### 1. File Permissions
```bash
# Kiểm tra quyền ghi trước khi redirect
if [ -w output.txt ]; then
    command > output.txt
else
    echo "Cannot write to file"
fi
```

### 2. Order of Redirections
```bash
# Sai
command 2>&1 > output.txt  # stderr vẫn ra console

# Đúng
command > output.txt 2>&1  # Cả stderr và stdout vào file
```

### 3. Pipe với sudo
```bash
# Sai
echo "content" | sudo tee > file  # Permission denied

# Đúng
echo "content" | sudo tee file > /dev/null
```

---

*Note: Tài liệu này dành cho mục đích học tập. Trong môi trường production cần thêm các xử lý bảo mật khác.*