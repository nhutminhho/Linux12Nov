# Lab Thực Hành: Giám Sát và Quản Lý Processes

## Phần 1: Làm Quen với ps Command

### Lab 1.1: Xem Processes Cơ Bản
```bash
# 1. Xem processes của terminal hiện tại
ps

# 2. Phân tích kết quả:
# - PID: ID của process
# - TTY: Terminal process đang chạy
# - TIME: Thời gian CPU sử dụng
# - CMD: Tên lệnh hoặc chương trình
```

### Lab 1.2: Xem Tất Cả Processes
```bash
# 1. Xem tất cả processes với format đầy đủ
ps -ef

# 2. Lọc thông tin cụ thể
# Xem processes của user root
ps -ef | grep ^root

# 3. Xem processes với format dễ đọc
ps aux
```

### Lab 1.3: Thực Hành với Cột Thông Tin
```bash
# 1. Xem thông tin chi tiết
ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu

# 2. Tìm process chiếm nhiều CPU nhất
ps -eo pid,ppid,cmd,%cpu --sort=-%cpu | head -5

# 3. Tìm process chiếm nhiều RAM nhất
ps -eo pid,ppid,cmd,%mem --sort=-%mem | head -5
```

## Phần 2: Thực Hành với top Command

### Lab 2.1: Giám Sát Hệ Thống Realtime
```bash
# 1. Chạy top
top

# 2. Trong top, thử các phím tắt:
# P: Sắp xếp theo %CPU
# M: Sắp xếp theo %MEM
# N: Sắp xếp theo PID
# Q: Thoát

# 3. Lọc processes theo user
# Nhấn 'u' trong top
# Nhập username
```

### Lab 2.2: Tùy Chỉnh Hiển Thị top
```bash
# 1. Trong top, nhấn 'f'
# 2. Chọn thêm/bớt cột hiển thị
# 3. Nhấn 'q' để quay lại
# 4. Nhấn 'W' để lưu cấu hình
```

## Phần 3: Tìm Hiểu Process State

### Lab 3.1: Tạo và Quan Sát Process States
```bash
# 1. Tạo process sleep
sleep 300 &

# 2. Xem trạng thái
ps -o pid,ppid,state,cmd $(pgrep sleep)

# 3. Tạo zombie process (thử nghiệm)
bash -c "sleep 5 & exit"
ps -eo pid,ppid,state,cmd | grep defunct
```

### Lab 3.2: Process Priority
```bash
# 1. Chạy lệnh với nice value
nice -n 10 sleep 100 &

# 2. Xem nice value
ps -o pid,ppid,ni,cmd $(pgrep sleep)

# 3. Thay đổi nice value (cần sudo)
sudo renice -n 15 -p $(pgrep sleep)
```

## Phần 4: Thực Hành Tìm và Quản Lý Process

### Lab 4.1: Tìm Process Cụ Thể
```bash
# 1. Sử dụng pidof
pidof sshd

# 2. Sử dụng pgrep
pgrep -l sshd

# 3. Tìm với ps và grep
ps aux | grep sshd
```

### Lab 4.2: Gửi Signals đến Process
```bash
# 1. Tạo process test
sleep 1000 &

# 2. Tạm dừng process
kill -STOP $(pgrep sleep)

# 3. Tiếp tục process
kill -CONT $(pgrep sleep)

# 4. Kết thúc process
kill -TERM $(pgrep sleep)
```

## Phần 5: Bài Tập Thực Hành

### Bài 1: Giám Sát System Load
1. Mở 3 terminal:
   - Terminal 1: chạy top
   - Terminal 2: chạy htop (cài đặt nếu cần)
   - Terminal 3: tạo load với lệnh:
   ```bash
   # Tạo CPU load
   yes > /dev/null &
   ```

2. Quan sát và so sánh:
   - CPU usage
   - Memory usage
   - Process states

### Bài 2: Quản Lý Process Priority
1. Tạo script test:
```bash
# Tạo file test.sh
cat > test.sh << 'EOF'
#!/bin/bash
while true; do
    echo "Running..."
    sleep 1
done
EOF

chmod +x test.sh
```

2. Chạy với các priority khác nhau:
```bash
# Chạy bình thường
./test.sh &

# Chạy với nice 10
nice -n 10 ./test.sh &

# Chạy với nice 19
nice -n 19 ./test.sh &
```

3. Quan sát và so sánh trong top

## Xử Lý Lỗi Thường Gặp

### 1. Process Không Kill Được
```bash
# Thử kill -9
kill -9 PID

# Kiểm tra process state
ps -o pid,state,cmd PID
```

### 2. Top Không Hiển Thị Đủ Thông Tin
```bash
# Reset cấu hình top
rm ~/.toprc

# Chạy lại top và cấu hình
top
```

### 3. Process Zombie
```bash
# Tìm zombie processes
ps aux | grep Z

# Tìm parent process
ps -o ppid= -p ZOMBIE_PID

# Kill parent nếu cần
kill -9 PARENT_PID
```

## Ghi Chú Quan Trọng

1. **Process States:**
   - R: Running
   - S: Sleeping
   - D: Uninterruptible sleep
   - Z: Zombie
   - T: Stopped

2. **Signals Thông Dụng:**
   - SIGTERM (15): Kết thúc gracefully
   - SIGKILL (9): Kết thúc force
   - SIGSTOP (19): Tạm dừng
   - SIGCONT (18): Tiếp tục

3. **Monitoring Tips:**
   - Sử dụng top cho realtime monitoring
   - ps aux cho thông tin chi tiết
   - pidof/pgrep cho tìm kiếm nhanh
   - Luôn check %CPU và %MEM

