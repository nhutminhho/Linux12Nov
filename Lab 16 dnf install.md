# Lab Thực Hành: Quản Lý Packages Trên Linux

## Phần 1: Tổng Quan về Package Management

### 1.1. Tại Sao Cần Package Management?
- **Mục đích:**
  - Quản lý phần mềm hiệu quả
  - Cài đặt/gỡ bỏ dễ dàng
  - Tự động xử lý dependencies
  - Cập nhật hệ thống an toàn

### 1.2. Các Công Cụ Chính
```bash
# 1. DNF (Mới nhất - thay thế YUM)
dnf --version

# 2. RPM (Công cụ cấp thấp)
rpm --version

# Giải thích:
# - DNF: Quản lý cao cấp, tự động xử lý dependencies
# - RPM: Quản lý cấp thấp, làm việc trực tiếp với packages
```

## Phần 2: Thực Hành với DNF

### Lab 2.1: Quản Lý Repository
```bash
# 1. Xem danh sách repos
dnf repolist

# 2. Xem thông tin repo chi tiết
dnf repoinfo

# 3. Enable/disable repo
sudo dnf config-manager --enable epel
sudo dnf config-manager --disable epel

# Giải thích:
# - Repos chứa các packages
# - EPEL cung cấp thêm packages
# - Config-manager quản lý cấu hình DNF
```

### Lab 2.2: Tìm Kiếm và Cài Đặt Packages
```bash
# 1. Tìm package
dnf search nginx

# 2. Xem thông tin package
dnf info nginx

# 3. Cài đặt package
sudo dnf install nginx -y

# Giải thích:
# - Search: Tìm trong tất cả repos
# - Info: Xem metadata của package
# - -y: Tự động yes cho prompts
```

### Lab 2.3: Cập Nhật Hệ Thống
```bash
# 1. Kiểm tra updates
dnf check-update

# 2. Cập nhật cụ thể
sudo dnf update nginx

# 3. Cập nhật toàn bộ
sudo dnf update -y

# Giải thích:
# - Check-update: Liệt kê packages cần update
# - Update specific: Chỉ cập nhật package cụ thể
# - Update all: Cập nhật toàn bộ hệ thống
```

## Phần 3: Thực Hành với RPM

### Lab 3.1: Quản Lý Packages với RPM
```bash
# 1. Liệt kê packages đã cài
rpm -qa

# 2. Tìm package cụ thể
rpm -qa | grep nginx

# 3. Xem thông tin chi tiết
rpm -qi nginx

# Giải thích:
# - -qa: Query all packages
# - -qi: Query info
# - RPM làm việc trực tiếp với package database
```

### Lab 3.2: Verify và Quản Lý Files
```bash
# 1. Verify package
rpm -V nginx

# 2. Liệt kê files của package
rpm -ql nginx

# 3. Tìm package sở hữu file
rpm -qf /usr/sbin/nginx

# Giải thích:
# - -V: Verify package integrity
# - -ql: List files
# - -qf: Find package owning file
```

## Phần 4: Thực Hành Nâng Cao

### Lab 4.1: Quản Lý Groups
```bash
# 1. Xem danh sách groups
dnf grouplist

# 2. Cài đặt group
sudo dnf groupinstall "Development Tools" -y

# 3. Xóa group
sudo dnf groupremove "Development Tools"

# Giải thích:
# - Groups: Tập hợp các packages liên quan
# - Development Tools: Công cụ phát triển
# - Tiện lợi khi cài nhiều packages liên quan
```

### Lab 4.2: Module Streams
```bash
# 1. Xem modules có sẵn
dnf module list

# 2. Enable module stream
sudo dnf module enable nodejs:18

# 3. Cài đặt từ module
sudo dnf module install nodejs:18

# Giải thích:
# - Modules: Phiên bản khác nhau của package
# - Streams: Các nhánh phiên bản
# - Linh hoạt trong việc chọn phiên bản
```

## Phần 5: Bài Tập Thực Hành

### Bài 1: Cài Đặt Web Server
```bash
# 1. Tìm và so sánh web servers
dnf search "web server"
dnf info nginx httpd

# 2. Cài đặt và cấu hình
sudo dnf install nginx -y
sudo systemctl enable --now nginx

# 3. Verify cài đặt
rpm -V nginx
curl localhost
```

### Bài 2: Quản Lý Dependencies
```bash
# 1. Xem dependencies
dnf repoquery --requires nginx

# 2. Xem reverse dependencies
dnf repoquery --whatrequires nginx

# 3. Cài đặt với dependencies cụ thể
sudo dnf install --setopt=install_weak_deps=False nginx
```

## Phần 6: Troubleshooting

### 6.1. Xử Lý Lỗi Thường Gặp
```bash
# 1. Cleanup cache
sudo dnf clean all

# 2. Rebuild cache
sudo dnf makecache

# 3. Fix broken dependencies
sudo dnf distro-sync
```

### 6.2. Debug và Logging
```bash
# 1. Debug mode
dnf -v update

# 2. Xem history
dnf history list

# 3. Undo thao tác
sudo dnf history undo [id]
```

## Best Practices

1. **Update Regular:**
   - Cập nhật thường xuyên
   - Kiểm tra trước khi update
   - Backup trước khi update lớn

2. **Repository Management:**
   - Chỉ dùng repos tin cậy
   - Disable repos không dùng
   - Verify GPG keys

3. **Performance:**
   - Clean cache định kỳ
   - Dùng fastest mirror
   - Tối ưu download parallel

