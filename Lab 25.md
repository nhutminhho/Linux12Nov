# Bài Tập Thực Hành: Cấu Hình Mạng Cơ Bản Trên Linux

## Bài Tập 1: Cấu Hình IP Tĩnh

### Yêu Cầu
1. Đặt IP tĩnh: 192.168.1.200
2. Subnet mask: 255.255.255.0
3. Gateway: 192.168.1.1
4. DNS: 8.8.8.8

### Bước 1: Kiểm Tra Cấu Hình Hiện Tại
```bash
# Xem thông tin mạng hiện tại
ip addr show

# Kết quả mong đợi:
# ens33: card mạng chính
# inet: địa chỉ IP hiện tại

# Giải thích:
- ip addr show: lệnh xem cấu hình mạng
- ens33: tên card mạng (có thể khác trên máy bạn)
```

### Bước 2: Backup Cấu Hình
```bash
# Tạo backup
sudo cp /etc/netplan/00-installer-config.yaml /etc/netplan/backup.yaml

# Giải thích:
- Backup để phòng khi cấu hình mới bị lỗi
- Có thể khôi phục lại dễ dàng
```

### Bước 3: Tạo File Cấu Hình Mới
```bash
# Mở file cấu hình
sudo nano /etc/netplan/00-installer-config.yaml

# Thêm nội dung sau:
network:
  version: 2
  ethernets:
    ens33:
      addresses:
        - 192.168.1.200/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8]

# Giải thích từng dòng:
- version: 2: phiên bản netplan
- ethernets: cấu hình cho card mạng
- addresses: địa chỉ IP muốn đặt
- gateway4: cổng mặc định
- nameservers: máy chủ DNS
```

### Bước 4: Áp Dụng Cấu Hình
```bash
# Kiểm tra cú pháp
sudo netplan try

# Nếu OK, áp dụng cấu hình
sudo netplan apply

# Giải thích:
- netplan try: test cấu hình trong 2 phút
- netplan apply: áp dụng cấu hình vĩnh viễn
```

### Bước 5: Kiểm Tra Kết Quả
```bash
# Xem IP mới
ip addr show ens33

# Test kết nối
ping 8.8.8.8
ping google.com

# Giải thích:
- ping 8.8.8.8: test kết nối internet
- ping google.com: test DNS resolver
```

## Bài Tập 2: Cấu Hình Card Mạng Ảo

### Yêu Cầu
1. Tạo interface ảo eth0:1
2. Đặt IP: 192.168.1.201

### Bước 1: Tạo Interface Ảo
```bash
# Thêm vào file cấu hình netplan
sudo nano /etc/netplan/00-installer-config.yaml

# Thêm cấu hình:
network:
  version: 2
  ethernets:
    ens33:
      addresses:
        - 192.168.1.200/24
        - 192.168.1.201/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8]

# Giải thích:
- Thêm IP thứ hai vào addresses
- Cùng dùng gateway với IP chính
```

### Bước 2: Áp Dụng Và Kiểm Tra
```bash
# Áp dụng cấu hình
sudo netplan apply

# Kiểm tra
ip addr show

# Test từng IP
ping -I 192.168.1.200 google.com
ping -I 192.168.1.201 google.com

# Giải thích:
- -I: chỉ định interface để ping
- Kiểm tra cả hai IP hoạt động
```

## Bài Tập 3: Troubleshooting

### Tình Huống 1: Không Ping Được Gateway
```bash
# Kiểm tra kết nối vật lý
ip link show ens33

# Kiểm tra routing
ip route show

# Giải thích:
- ip link: xem trạng thái card mạng
- ip route: xem bảng định tuyến
```

### Tình Huống 2: Không Phân Giải Được DNS
```bash
# Kiểm tra file resolv.conf
cat /etc/resolv.conf

# Test DNS server
nslookup google.com 8.8.8.8

# Giải thích:
- resolv.conf: file cấu hình DNS
- nslookup: công cụ test DNS
```

## Tips và Mẹo Thực Hành

### 1. Kiểm Tra Trước Khi Cấu Hình
```bash
# Lưu thông tin cũ
ip addr show > ~/network-before.txt
ip route show > ~/route-before.txt

# Giải thích:
- Lưu lại để so sánh sau khi thay đổi
- Dễ dàng phát hiện vấn đề
```

### 2. Debug Hiệu Quả
```bash
# Xem log realtime
sudo tail -f /var/log/syslog

# Kiểm tra chi tiết card mạng
sudo ethtool ens33

# Giải thích:
- syslog: chứa thông tin lỗi
- ethtool: thông tin chi tiết về card mạng
```

### 3. Backup và Restore
```bash
# Backup toàn bộ /etc/netplan
sudo tar czf ~/netplan-backup.tar.gz /etc/netplan/

# Restore khi cần
sudo tar xzf ~/netplan-backup.tar.gz -C /

# Giải thích:
- Backup trước khi thay đổi lớn
- Có thể restore nhanh chóng
```

### 4. Best Practices
1. **Ghi Chép:**
   - Lưu lại mọi thay đổi
   - Ghi rõ ngày giờ
   - Lý do thay đổi

2. **Test Kỹ:**
   - Dùng netplan try trước
   - Test đầy đủ các tính năng
   - Kiểm tra hiệu năng

3. **Bảo Mật:**
   - Giới hạn quyền truy cập file cấu hình
   - Cấu hình firewall
   - Monitor traffic

