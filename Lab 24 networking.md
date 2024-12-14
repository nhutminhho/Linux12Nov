# Hướng Dẫn Cơ Bản về Mạng Máy Tính

## Phần 1: Các Khái Niệm Cơ Bản

### 1.1. Địa Chỉ IP (IP Address)

**Khái niệm:**
- Là địa chỉ định danh mỗi thiết bị trong mạng
- Giống như địa chỉ nhà để gửi thư

**Cách kiểm tra IP trên Linux:**
```bash
# Xem IP của máy
ip addr show
# hoặc
ifconfig

# Giải thích kết quả:
# ens33: tên card mạng
# inet: địa chỉ IPv4
# inet6: địa chỉ IPv6
```

### 1.2. Subnet Mask
```bash
# Ví dụ:
IP: 192.168.1.100
Subnet Mask: 255.255.255.0

# Kiểm tra subnet
ip route show

# Giải thích:
- 255.255.255.0 = 24 bit network
- Cho phép 254 hosts trong mạng
- Network ID: 192.168.1.0
```

### 1.3. Gateway
```bash
# Xem gateway
ip route show | grep default
# hoặc
route -n

# Giải thích:
- Gateway là cổng ra internet
- Thường là địa chỉ router
- VD: 192.168.1.1
```

## Phần 2: Thực Hành Cơ Bản

### 2.1. Kiểm Tra Kết Nối Mạng
```bash
# 1. Kiểm tra card mạng
ip link show

# 2. Kiểm tra IP
ip addr show

# 3. Test kết nối
ping 8.8.8.8

# Giải thích:
- ip link: xem trạng thái card mạng
- ip addr: xem địa chỉ IP
- ping: kiểm tra kết nối
```

### 2.2. Cấu Hình IP Tĩnh (Static IP)
```bash
# 1. Backup cấu hình cũ
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/00-installer-config.yaml.backup

# 2. Tạo file cấu hình mới
sudo nano /etc/netplan/00-installer-config.yaml

# 3. Thêm nội dung:
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]

# 4. Áp dụng cấu hình
sudo netplan apply

# Giải thích:
- addresses: IP tĩnh muốn đặt
- gateway4: địa chỉ router
- nameservers: DNS servers
```

### 2.3. Cấu Hình DHCP
```bash
# 1. Sửa file cấu hình
sudo nano /etc/netplan/00-installer-config.yaml

# 2. Nội dung:
network:
  version: 2
  renderer: networkd
  ethernets:
    ens33:
      dhcp4: true

# 3. Áp dụng
sudo netplan apply

# Giải thích:
- dhcp4: true = nhận IP tự động
- Đơn giản hơn static IP
- Phù hợp môi trường home/test
```

## Phần 3: Thực Hành Nâng Cao

### 3.1. Kiểm Tra Thông Tin Mạng Chi Tiết
```bash
# 1. Xem interfaces
ip -s link show

# 2. Xem routing table
ip route show

# 3. Xem DNS
cat /etc/resolv.conf

# Giải thích:
- -s: hiển thị statistics
- route: bảng định tuyến
- resolv.conf: cấu hình DNS
```

### 3.2. Troubleshooting Cơ Bản
```bash
# 1. Kiểm tra kết nối
ping google.com

# 2. Kiểm tra DNS
nslookup google.com

# 3. Kiểm tra route
traceroute google.com

# Giải thích:
- ping: test kết nối cơ bản
- nslookup: kiểm tra DNS
- traceroute: xem đường đi gói tin
```

## Phần 4: Bài Tập Thực Hành

### Bài 1: Cấu Hình Mạng Cơ Bản
```bash
# Yêu cầu:
1. Đặt IP tĩnh: 192.168.1.200
2. Subnet mask: 255.255.255.0
3. Gateway: 192.168.1.1
4. DNS: 8.8.8.8

# Các bước thực hiện:
1. Backup cấu hình
2. Tạo cấu hình mới
3. Test và verify
```

### Bài 2: Test Network
```bash
# 1. Kiểm tra kết nối local
ping localhost

# 2. Kiểm tra card mạng
sudo ethtool ens33

# 3. Kiểm tra ports
sudo netstat -tulpn

# Mục đích:
- Hiểu cách network hoạt động
- Biết cách debug
- Nắm được các tools cơ bản
```

## Tips và Lưu Ý

### 1. Bảo Mật
- Luôn backup trước khi thay đổi
- Cẩn thận với firewall rules
- Kiểm tra log thường xuyên

### 2. Performance
```bash
# Kiểm tra network speed
speedtest-cli

# Monitor network traffic
iftop
```

### 3. Debugging
```bash
# Xem log
sudo tail -f /var/log/syslog

# Kiểm tra card mạng
dmesg | grep eth
```

### 4. Best Practices
1. Documentation:
   - Ghi chép cấu hình
   - Lưu các thay đổi
   - Document troubleshooting steps

2. Monitoring:
   - Theo dõi traffic
   - Alert khi có vấn đề
   - Backup cấu hình

3. Maintenance:
   - Update firmware
   - Kiểm tra cables
   - Test backup plans

