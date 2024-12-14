# Lab Thực Hành: Tìm Hiểu Mô Hình OSI

## Phần 1: Kiểm Tra Các Lớp OSI Qua Wireshark

### Bước 1: Chuẩn Bị Môi Trường
```bash
# Cài đặt Wireshark
sudo apt install wireshark

# Cài đặt các công cụ network
sudo apt install net-tools traceroute ping

# Giải thích:
- Wireshark: công cụ phân tích gói tin
- net-tools: các công cụ quản lý mạng
```

## Phần 2: Phân Tích Từng Lớp

### Layer 1 - Physical
```bash
# Kiểm tra card mạng
ip link show

# Kết quả:
# ens33: <BROADCAST,MULTICAST,UP,LOWER_UP>
#        link/ether 00:0c:29:xx:xx:xx

# Giải thích:
- UP: card mạng hoạt động
- LOWER_UP: kết nối vật lý OK
- link/ether: địa chỉ MAC
```

### Layer 2 - Data Link
```bash
# Xem MAC address
ip link show ens33

# Xem ARP table
arp -a

# Giải thích:
- MAC address: định danh thiết bị
- ARP: phân giải IP sang MAC
```

### Layer 3 - Network
```bash
# Kiểm tra định tuyến
ip route show

# Test kết nối
ping google.com
traceroute google.com

# Giải thích:
- route: đường đi gói tin
- ping: kiểm tra kết nối
- traceroute: xem đường đi cụ thể
```

### Layer 4 - Transport
```bash
# Xem các kết nối TCP/UDP
netstat -tuln

# Giải thích:
- -t: hiện TCP
- -u: hiện UDP
- -l: chỉ hiện đang lắng nghe
- -n: hiện số port
```

### Layer 5 - Session
```bash
# Kiểm tra các phiên kết nối
netstat -np

# Test tạo kết nối
telnet google.com 80

# Giải thích:
- netstat: hiện các kết nối
- telnet: tạo phiên TCP
```

### Layer 6 - Presentation
```bash
# Test SSL/TLS
openssl s_client -connect google.com:443

# Giải thích:
- SSL/TLS: mã hóa dữ liệu
- s_client: test kết nối SSL
```

### Layer 7 - Application
```bash
# Test HTTP
curl http://example.com

# Test DNS
dig google.com

# Giải thích:
- curl: gửi request HTTP
- dig: truy vấn DNS
```

## Phần 3: Bài Tập Thực Hành

### Bài 1: Phân Tích Kết Nối Web

#### Yêu Cầu:
1. Bắt gói tin khi truy cập web
2. Phân tích theo từng lớp OSI

#### Các Bước Thực Hiện:
```bash
# 1. Khởi động Wireshark
sudo wireshark

# 2. Chọn interface
Click vào ens33

# 3. Mở trình duyệt
firefox google.com

# 4. Trong Wireshark, phân tích:
- Layer 2: Xem MAC source và destination
- Layer 3: Xem IP source và destination
- Layer 4: Xem port numbers
- Layer 7: Xem HTTP headers
```

### Bài 2: Debug Network Issue

#### Tình Huống:
Không thể truy cập web server

#### Các Bước Kiểm Tra:
```bash
# 1. Layer 1 & 2
ip link show
# Kiểm tra dây mạng và card mạng

# 2. Layer 3
ping web-server-ip
# Kiểm tra kết nối IP

# 3. Layer 4
netstat -tuln | grep 80
# Kiểm tra web server có lắng nghe

# 4. Layer 7
curl -v web-server-ip
# Test HTTP request
```

## Phần 4: Tips và Best Practices

### 1. Kiểm Tra Từ Dưới Lên
```bash
# Layer 1: Physical
ip link show

# Layer 2: Data Link
arp -a

# Layer 3: Network
ping

# Layer 4-7: Application
curl, dig, etc.

# Giải thích:
- Kiểm tra từ thấp lên cao
- Dễ xác định vấn đề
- Tiết kiệm thời gian
```

### 2. Công Cụ Debug Theo Layer
```bash
# Layer 1-2:
ethtool, ip link

# Layer 3:
ip route, traceroute

# Layer 4:
netstat, ss

# Layer 7:
tcpdump, wireshark

# Giải thích:
- Mỗi layer có công cụ riêng
- Kết hợp các công cụ để debug
```

### 3. Lưu Ý Quan Trọng

1. **Physical (Layer 1)**
   - Kiểm tra đèn card mạng
   - Kiểm tra dây cáp
   - Kiểm tra cổng switch

2. **Data Link (Layer 2)**
   - MAC address đúng
   - Switch port đúng
   - VLAN config đúng

3. **Network (Layer 3)**
   - IP đúng subnet
   - Default gateway đúng
   - Routing table đúng

4. **Transport (Layer 4)**
   - Port đúng
   - Firewall mở port
   - Service đang chạy

5. **Application (Layer 7)**
   - Config đúng
   - Log errors
   - Test từng service

