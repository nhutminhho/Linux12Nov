# Lab Thực Hành: Cấu Hình Và Kiểm Tra Mạng

## Lab 1: Khám Phá Network Interface

### Mục Đích
- Hiểu về card mạng và cách đặt tên
- Biết cách xem và thay đổi cấu hình
- Hiểu file cấu hình mạng

### Bước 1: Xem Thông Tin Card Mạng
```bash
# Xem danh sách card mạng
ip addr show

# Giải thích kết quả:
lo: Card loopback - giao tiếp nội bộ
ens33: Card mạng vật lý/ảo (tên có thể khác)

# Tại sao cần xem?
1. Biết tên card mạng để cấu hình
2. Kiểm tra IP đã được cấp chưa
3. Xem trạng thái kết nối
```

### Bước 2: Tìm Hiểu File Cấu Hình
```bash
# Xem file cấu hình
cd /etc/sysconfig/network-scripts/
ls ifcfg-*

# Mở file cấu hình
cat ifcfg-ens33

# Giải thích từng tham số:
DEVICE=ens33         # Tên thiết bị
BOOTPROTO=dhcp/none # Cách lấy IP
ONBOOT=yes          # Tự động bật khi boot
IPADDR=192.168.1.100 # IP tĩnh (nếu dùng)
```

## Lab 2: Cấu Hình Hostname và DNS

### Mục Đích
- Hiểu về hostname và DNS
- Biết cách cấu hình tên máy
- Cấu hình phân giải tên miền

### Bước 1: Quản Lý Hostname
```bash
# Xem hostname hiện tại
hostname

# Đặt hostname mới
sudo hostnamectl set-hostname myserver

# Giải thích:
- Hostname giúp định danh máy trong mạng
- Dễ nhận biết và quản lý
- Cần unique trong mạng
```

### Bước 2: Cấu Hình DNS
```bash
# Xem cấu hình DNS
cat /etc/resolv.conf

# Thêm DNS server
sudo nano /etc/resolv.conf
nameserver 8.8.8.8
nameserver 8.8.4.4

# Giải thích:
- DNS giúp phân giải tên miền
- 8.8.8.8 là DNS của Google
- Cần ít nhất 2 DNS server
```

## Lab 3: Kiểm Tra Kết Nối

### Mục Đích
- Học cách test kết nối
- Hiểu về các công cụ network
- Biết cách troubleshoot

### Bước 1: Sử Dụng Ping
```bash
# Test kết nối local
ping localhost

# Test kết nối internet
ping -c 4 google.com

# Giải thích:
- -c 4: gửi 4 gói tin rồi dừng
- Kiểm tra:
  + Độ trễ (latency)
  + Mất gói (packet loss)
  + Kết nối có thông không
```

### Bước 2: Sử Dụng Traceroute
```bash
# Xem đường đi gói tin
traceroute google.com

# Giải thích kết quả:
1  192.168.1.1     # Gateway nhà
2  172.16.1.1      # Router ISP
3  ...             # Các hop trung gian
* * *              # Hop không phản hồi

# Tại sao dùng traceroute:
1. Xác định điểm nghẽn
2. Hiểu đường đi gói tin
3. Debug vấn đề kết nối
```

## Lab 4: Bài Tập Thực Hành

### Bài 1: Cấu Hình IP Tĩnh
```bash
# 1. Backup file cấu hình
sudo cp ifcfg-ens33 ifcfg-ens33.backup

# 2. Sửa file cấu hình
sudo nano ifcfg-ens33

# 3. Thêm/sửa các dòng:
BOOTPROTO=none
IPADDR=192.168.1.100
NETMASK=255.255.255.0
GATEWAY=192.168.1.1
DNS1=8.8.8.8

# 4. Khởi động lại mạng
sudo systemctl restart NetworkManager

# Giải thích:
- Backup phòng khi lỗi
- IP tĩnh ổn định hơn DHCP
- Cần đúng các thông số mạng
```

### Bài 2: Test và Verify
```bash
# 1. Kiểm tra IP
ip addr show ens33

# 2. Test kết nối local
ping localhost

# 3. Test kết nối gateway
ping 192.168.1.1

# 4. Test kết nối internet
ping 8.8.8.8
ping google.com

# Giải thích:
- Test từ gần đến xa
- Xác định chính xác điểm lỗi
- Đảm bảo mọi kết nối OK
```

## Tips Quan Trọng

### 1. Các File Cấu Hình Quan Trọng
```plaintext
/etc/sysconfig/network-scripts/ifcfg-*  # Cấu hình card mạng
/etc/hostname                           # Tên máy
/etc/hosts                             # DNS local
/etc/resolv.conf                       # DNS servers
```

### 2. Các Lệnh Thường Dùng
```bash
ip addr      # Xem thông tin mạng
ping         # Test kết nối
traceroute   # Xem đường đi
netstat -r   # Xem bảng định tuyến
route        # Xem/sửa định tuyến
```

### 3. Best Practices
1. **Backup trước khi sửa:**
   ```bash
   cp file file.backup
   ```

2. **Test sau mỗi thay đổi:**
   ```bash
   ping gateway
   ping 8.8.8.8
   ```

3. **Ghi chép cấu hình:**
   - Lưu các thay đổi
   - Ghi thời gian thay đổi
   - Lý do thay đổi

