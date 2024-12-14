# Lab Cơ Bản: Tìm Hiểu Mô Hình OSI Qua Thực Hành

## Lab 1: Khám Phá Layer 1 - Physical Layer

### Mục Đích
- Hiểu về kết nối vật lý
- Xác định vấn đề ở tầng vật lý
- Học cách kiểm tra kết nối

### Thực Hành
```bash
# 1. Kiểm tra trạng thái card mạng
ip link show

# Kết quả có thể thấy:
ens33: <BROADCAST,MULTICAST,UP,LOWER_UP>

# Giải thích:
- UP: card mạng đã bật
- LOWER_UP: có kết nối vật lý
- DOWN: không có kết nối
```

### Tại Sao Cần Kiểm Tra Layer 1?
1. Đây là tầng cơ bản nhất
2. Nếu Layer 1 lỗi, các tầng trên không thể hoạt động
3. Dễ xảy ra lỗi nhất (dây đứt, hỏng cổng,...)

## Lab 2: Khám Phá Layer 2 - Data Link Layer

### Mục Đích
- Hiểu về địa chỉ MAC
- Học cách máy tính tìm nhau trong mạng LAN
- Hiểu về ARP

### Thực Hành
```bash
# 1. Xem địa chỉ MAC
ip link show ens33

# 2. Xem bảng ARP
arp -a

# Giải thích:
- MAC là định danh duy nhất của card mạng
- ARP giúp tìm MAC của máy khác qua IP
```

### Tại Sao Cần Hiểu Layer 2?
1. Hoạt động trong mạng LAN
2. Switch hoạt động ở layer này
3. Cần cho troubleshoot vấn đề mạng nội bộ

## Lab 3: Khám Phá Layer 3 - Network Layer

### Mục Đích
- Hiểu về địa chỉ IP
- Học cách máy tính tìm đường đi
- Thực hành với routing

### Thực Hành Đơn Giản
```bash
# 1. Xem địa chỉ IP
ip addr show

# 2. Kiểm tra kết nối
ping 8.8.8.8

# 3. Xem đường đi
traceroute 8.8.8.8

# Giải thích:
- ip addr: xem cấu hình IP
- ping: kiểm tra kết nối
- traceroute: xem đường đi gói tin
```

### Tại Sao Layer 3 Quan Trọng?
1. Xác định vị trí máy trong mạng
2. Quyết định đường đi của gói tin
3. Cần thiết cho kết nối Internet

## Lab 4: Bài Tập Thực Hành Đơn Giản

### Bài 1: Kiểm Tra Kết Nối Cơ Bản
```bash
# Bước 1: Kiểm tra card mạng
ip link show
# Kết quả mong đợi: UP, LOWER_UP

# Bước 2: Kiểm tra IP
ip addr show
# Xem có IP không và đúng dải không

# Bước 3: Test kết nối
ping google.com
# Kiểm tra có thông không

# Giải thích từng bước:
1. UP = card mạng hoạt động
2. Có IP = đã được cấu hình mạng
3. Ping được = kết nối internet OK
```

### Bài 2: Tìm Lỗi Theo Tầng
```bash
# Kịch bản: Không vào được web

# Bước 1: Layer 1
ip link show
# Kiểm tra dây và card mạng

# Bước 2: Layer 2
arp -a
# Kiểm tra kết nối LAN

# Bước 3: Layer 3
ping 8.8.8.8
# Kiểm tra kết nối Internet

# Giải thích:
- Kiểm tra từ thấp lên cao
- Xác định chính xác lớp bị lỗi
- Sửa đúng vấn đề
```

## Tips Quan Trọng

### 1. Debugging Theo Thứ Tự
```bash
# Luôn bắt đầu từ Layer 1:
1. Kiểm tra đèn card mạng
2. Kiểm tra dây mạng
3. Kiểm tra cổng switch

# Sau đó lên Layer 2:
1. Kiểm tra MAC
2. Kiểm tra ARP
3. Kiểm tra switch port

# Tiếp đến Layer 3:
1. Kiểm tra IP
2. Kiểm tra gateway
3. Kiểm tra routing
```

### 2. Công Cụ Cần Thiết
```bash
# Layer 1-2:
ip link
ethtool

# Layer 3:
ip addr
ping
traceroute

# Giải thích:
- Mỗi lệnh phục vụ mục đích khác nhau
- Kết hợp để có cái nhìn tổng quan
- Giúp xác định vấn đề nhanh chóng
```

### 3. Lưu Ý Khi Thực Hành

1. **An Toàn:**
   - Backup cấu hình trước khi thay đổi
   - Test trên môi trường không quan trọng
   - Ghi chép mọi thay đổi

2. **Hiệu Quả:**
   - Làm theo thứ tự các layer
   - Dùng đúng công cụ cho từng layer
   - Đọc kỹ kết quả trả về

3. **Thực Tế:**
   - Hầu hết vấn đề ở Layer 1-3
   - Layer 1 thường gặp nhất
   - Kiểm tra đơn giản trước, phức tạp sau

