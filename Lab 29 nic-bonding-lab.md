# Lab Thực Hành: Cấu Hình NIC Bonding

## Phần 1: Tìm Hiểu Về NIC Bonding

### NIC Bonding Là Gì?
- Kết hợp nhiều card mạng thành một
- Tăng tốc độ và độ tin cậy
- Dự phòng khi một card hỏng

### Tại Sao Cần NIC Bonding?
1. **Tăng Băng Thông:**
   - Kết hợp 2 card 1Gbps = 2Gbps
   - Phân tải tốt hơn

2. **Dự Phòng:**
   - Một card hỏng, card còn lại hoạt động
   - Không bị gián đoạn dịch vụ

## Phần 2: Chuẩn Bị Môi Trường

### Bước 1: Thêm Card Mạng
```bash
# 1. Tắt máy ảo
sudo shutdown -h now

# 2. Trong VirtualBox:
- Settings -> Network
- Adapter 2 -> Enable -> Bridge
- Adapter 3 -> Enable -> Bridge

# 3. Khởi động máy ảo và kiểm tra
ip addr show

# Giải thích:
- Cần ít nhất 2 card mạng cho bonding
- Bridge mode để card hoạt động độc lập
- Kiểm tra để đảm bảo hệ thống nhận card
```

### Bước 2: Kiểm Tra NetworkManager
```bash
# Kiểm tra trạng thái
systemctl status NetworkManager

# Xem danh sách interfaces
nmcli dev status

# Giải thích:
- NetworkManager quản lý kết nối mạng
- nmcli là công cụ dòng lệnh để cấu hình
- Cần đảm bảo service đang chạy
```

## Phần 3: Cấu Hình Bonding

### Bước 1: Load Bonding Module
```bash
# Load module
sudo modprobe bonding

# Kiểm tra
modinfo bonding

# Giải thích:
- modprobe thêm module vào kernel
- bonding module cần cho NIC bonding
- modinfo xem thông tin chi tiết module
```

### Bước 2: Tạo Bond Interface
```bash
# Tạo bond0
sudo nmcli con add type bond \
     con-name bond0 \
     ifname bond0 \
     mode balance-rr \
     ipv4.addresses 192.168.1.120/24 \
     ipv4.gateway 192.168.1.1

# Giải thích các tham số:
- type bond: loại kết nối
- con-name: tên connection
- ifname: tên interface
- mode balance-rr: chế độ cân bằng tải
- ipv4.addresses: địa chỉ IP cho bond
```

### Bước 3: Thêm Slave Interfaces
```bash
# Thêm card mạng 1
sudo nmcli con add type bond-slave \
     ifname enp0s8 \
     master bond0

# Thêm card mạng 2
sudo nmcli con add type bond-slave \
     ifname enp0s9 \
     master bond0

# Giải thích:
- bond-slave: card thành viên của bond
- master bond0: thuộc về bond0
- Cấu hình tự động cho slave
```

### Bước 4: Kích Hoạt Bond
```bash
# Bật bond
sudo nmcli con up bond0

# Kiểm tra
nmcli con show | grep -E 'bond0|enp0s8|enp0s9'

# Giải thích:
- up bond0: kích hoạt kết nối
- grep kiểm tra các kết nối liên quan
- Đảm bảo mọi thứ hoạt động
```

## Phần 4: Kiểm Tra và Xác Minh

### Bước 1: Kiểm Tra Trạng Thái
```bash
# Xem trạng thái bond
cat /proc/net/bonding/bond0

# Kiểm tra IP
ip addr show bond0

# Giải thích:
- /proc/net/bonding: thông tin realtime
- ip addr: xem cấu hình IP
- Đảm bảo bond hoạt động đúng
```

### Bước 2: Test Hoạt Động
```bash
# Test kết nối
ping -I bond0 8.8.8.8

# Test failover
sudo ifdown enp0s8
ping -c 4 8.8.8.8

# Giải thích:
- -I bond0: ping qua interface bond
- ifdown test tính năng failover
- Đảm bảo dự phòng hoạt động
```

## Tips và Lưu Ý

### 1. Các Mode Bonding Phổ Biến
```plaintext
balance-rr: Round-robin
active-backup: Dự phòng
balance-xor: Phân tải theo thuật toán
broadcast: Truyền qua mọi interface
```

### 2. Xử Lý Sự Cố
```bash
# Kiểm tra log
journalctl -u NetworkManager

# Xem trạng thái chi tiết
nmcli -p con show bond0
```

### 3. Backup và Restore
```bash
# Backup cấu hình
tar czf ~/network-backup.tar.gz /etc/sysconfig/network-scripts/

# Restore nếu cần
tar xzf ~/network-backup.tar.gz -C /
```

### 4. Best Practices
1. **Luôn Backup:**
   - Backup trước khi thay đổi
   - Lưu cấu hình cũ
   - Document mọi thay đổi

2. **Monitoring:**
   - Theo dõi trạng thái bond
   - Kiểm tra định kỳ
   - Alert khi có failover

3. **Testing:**
   - Test kỹ trước khi áp dụng
   - Có kế hoạch rollback
   - Verify sau mỗi thay đổi

