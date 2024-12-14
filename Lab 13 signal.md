# Lab Thực Hành: Process Priority và Signal Management

## Phần 1: Tìm Hiểu Process Priority (Niceness)

### Lab 1.1: Tìm Hiểu Nice Values
```bash
# Xem nice value mặc định
nice

# Giải thích:
# - Kết quả 0 là nice value mặc định
# - Nice value từ -20 (cao nhất) đến +19 (thấp nhất)
# - Số càng thấp, priority càng cao
# - Chỉ root mới set được nice value âm
```

### Lab 1.2: Xem Priority của Processes
```bash
# Xem processes với priority
ps -el

# Giải thích các cột:
# PRI: Priority - ưu tiên của process
# NI: Nice value - giá trị nice
# Công thức: PRI = 80 + NI
# => Nice value càng cao, priority càng thấp
```

### Tại Sao Cần Nice Value?
1. **Quản lý tài nguyên:**
   - Phân bổ CPU hợp lý cho các processes
   - Đảm bảo processes quan trọng được ưu tiên
   - Tránh processes không quan trọng chiếm quá nhiều CPU

2. **Tối ưu hóa hệ thống:**
   - Cải thiện performance tổng thể
   - Giảm độ trễ cho processes quan trọng
   - Cân bằng tải hệ thống

## Phần 2: Thực Hành với Nice Command

### Lab 2.1: Chạy Process với Nice Value Khác Nhau
```bash
# Tạo script test CPU
cat > cpu_test.sh << 'EOF'
#!/bin/bash
while true; do
    echo "Process running..."
    sleep 1
done
EOF
chmod +x cpu_test.sh

# Chạy với các nice value khác nhau
# 1. Chạy bình thường
./cpu_test.sh &

# 2. Chạy với nice value thấp hơn (ưu tiên cao hơn)
sudo nice -n -10 ./cpu_test.sh &

# 3. Chạy với nice value cao hơn (ưu tiên thấp hơn)
nice -n 10 ./cpu_test.sh &
```

### Tại Sao Cần Thử Nghiệm Nhiều Nice Values?
1. **Hiểu tác động của priority:**
   - Thấy được sự khác biệt về CPU time
   - Quan sát ảnh hưởng đến processes khác
   - Học cách cân bằng tài nguyên

2. **Tìm giá trị phù hợp:**
   - Xác định nice value tối ưu cho từng loại process
   - Tránh ảnh hưởng đến system performance
   - Hiểu về resource scheduling

## Phần 3: Thực Hành với Renice

### Lab 3.1: Thay Đổi Nice Value của Process Đang Chạy
```bash
# 1. Chạy process test
./cpu_test.sh &

# 2. Tìm PID
pidof cpu_test.sh

# 3. Thay đổi nice value
sudo renice -n -5 -p $(pidof cpu_test.sh)

# Giải thích:
# - renice cho phép thay đổi priority khi process đang chạy
# - Hữu ích khi cần điều chỉnh system load
# - Không cần restart process
```

### Tại Sao Cần Renice?
1. **Điều chỉnh linh hoạt:**
   - Thay đổi priority mà không cần dừng process
   - Phản ứng nhanh với system load
   - Tối ưu hóa realtime

2. **Xử lý tình huống:**
   - Giải quyết high load
   - Ưu tiên processes quan trọng
   - Debug performance issues

## Phần 4: Thực Hành với Signals

### Lab 4.1: Tìm Hiểu Các Signals
```bash
# Xem danh sách signals
kill -l

# Các signals quan trọng:
# SIGHUP (1): Reload configuration
# SIGINT (2): Interrupt (Ctrl+C)
# SIGKILL (9): Force kill
# SIGTERM (15): Graceful termination
```

### Lab 4.2: Thực Hành với Signals
```bash
# 1. Tạo process test
./cpu_test.sh &

# 2. Gửi SIGTERM (graceful)
kill -15 $(pidof cpu_test.sh)

# 3. Gửi SIGKILL (force)
kill -9 $(pidof cpu_test.sh)
```

### Tại Sao Cần Hiểu về Signals?
1. **Process Management:**
   - Điều khiển processes hiệu quả
   - Xử lý processes treo
   - Clean up resources đúng cách

2. **System Administration:**
   - Quản lý services
   - Xử lý lỗi
   - Bảo trì hệ thống

## Phần 5: Bài Tập Thực Hành

### Bài 1: CPU Load Test
```bash
# 1. Tạo script tạo load
cat > load.sh << 'EOF'
#!/bin/bash
while true; do
    echo "$(date): Process working..." >> load.log
    for i in {1..1000000}; do
        echo "Computing $i" > /dev/null
    done
done
EOF
chmod +x load.sh

# 2. Chạy với các nice value khác nhau
./load.sh &  # Normal
nice -n 10 ./load.sh &  # Low priority
sudo nice -n -10 ./load.sh &  # High priority

# 3. Quan sát trong top
top

# 4. So sánh CPU usage
```

### Bài 2: Signal Handling
```bash
# 1. Tạo script xử lý signal
cat > signal_test.sh << 'EOF'
#!/bin/bash
trap "echo 'Caught SIGTERM'" SIGTERM
trap "echo 'Caught SIGHUP'" SIGHUP

echo "PID: $$"
while true; do
    echo "Running..."
    sleep 1
done
EOF
chmod +x signal_test.sh

# 2. Thử nghiệm các signals
./signal_test.sh &
kill -SIGTERM $(pidof signal_test.sh)
kill -SIGHUP $(pidof signal_test.sh)
```

## Ghi Chú Quan Trọng

### 1. Best Practices
- Sử dụng SIGTERM trước SIGKILL
- Cẩn thận khi set negative nice values
- Kiểm tra system load thường xuyên

### 2. Troubleshooting
- Dùng top để monitor priorities
- Check process state trước khi kill
- Log các thay đổi priority

### 3. Security
- Chỉ root mới set negative nice values
- Cẩn thận với SIGKILL
- Kiểm soát quyền renice

