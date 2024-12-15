# Lab: Triển khai 3 node AlmaLinux với Vagrant

## Mục tiêu
- Tạo 3 máy ảo chạy AlmaLinux 9
- Thiết lập mạng private giữa các máy
- Cấu hình cơ bản và tự động hóa với Vagrant

## Yêu cầu
- VirtualBox đã được cài đặt
- Vagrant đã được cài đặt
- Khoảng 6GB RAM trống cho 3 máy ảo

## Phần 1: Chuẩn bị môi trường

### Bước 1: Tạo thư mục làm việc
```bash
mkdir vagrant-lab
cd vagrant-lab
```

### Bước 2: Tạo file Vagrantfile
Tạo file `Vagrantfile` với nội dung sau:

```ruby
Vagrant.configure("2") do |config|
  # Thiết lập mạng private chung cho tất cả các máy
  config.vm.network "private_network", type: "dhcp"

  # Cấu hình máy Control
  config.vm.define "control" do |control|
    # Sử dụng box AlmaLinux 9
    control.vm.box = "almalinux/9"
    # Đặt hostname cho máy
    control.vm.hostname = "control"
    # Cấu hình IP tĩnh trong mạng private
    control.vm.network "private_network", ip: "192.168.56.10"
    # Cấu hình tài nguyên cho máy ảo
    control.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"  # 2GB RAM
      vb.cpus = 2         # 2 CPU
    end
  end

  # Cấu hình Node 1 - tương tự như Control
  config.vm.define "node1" do |node1|
    node1.vm.box = "almalinux/9"
    node1.vm.hostname = "node1"
    node1.vm.network "private_network", ip: "192.168.56.11"
    node1.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end

  # Cấu hình Node 2 - tương tự như Control và Node 1
  config.vm.define "node2" do |node2|
    node2.vm.box = "almalinux/9"
    node2.vm.hostname = "node2"
    node2.vm.network "private_network", ip: "192.168.56.12"
    node2.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end
  end

  # Script tự động chạy khi máy được tạo
  config.vm.provision "shell", inline: <<-SHELL
    # Cập nhật tất cả các gói phần mềm
    dnf update -y
    
    # Cài đặt các công cụ cần thiết:
    # - vim: editor để chỉnh sửa file
    # - net-tools: công cụ network (ifconfig, netstat)
    # - wget: tải file
    # - curl: test API và tải file
    dnf install -y vim net-tools wget curl

    # Tắt SELinux để tránh các vấn đề về permission
    setenforce 0
    # Cấu hình SELinux permissive khi khởi động lại
    sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config

    # Thêm các entry vào /etc/hosts để các máy có thể resolve tên của nhau
    echo "192.168.56.10 control" >> /etc/hosts
    echo "192.168.56.11 node1" >> /etc/hosts
    echo "192.168.56.12 node2" >> /etc/hosts
  SHELL
end
```

## Phần 2: Triển khai môi trường

### Bước 1: Khởi tạo môi trường
```bash
vagrant up
```
Lệnh này sẽ:
1. Tải box AlmaLinux 9 về máy (nếu chưa có)
2. Tạo 3 máy ảo theo cấu hình
3. Cấu hình mạng cho các máy
4. Chạy script provision

### Bước 2: Kiểm tra trạng thái các máy
```bash
vagrant status
```

### Bước 3: Kết nối vào máy control
```bash
vagrant ssh control
```

### Bước 4: Kiểm tra kết nối
Sau khi vào máy control:
```bash
# Kiểm tra IP
ip addr show

# Ping thử đến node1 và node2
ping -c 3 node1
ping -c 3 node2

# Kiểm tra các công cụ đã cài đặt
which vim
which curl
which wget
```

### Bước 5: Kiểm tra SELinux
```bash
# Kiểm tra trạng thái SELinux
getenforce
```

## Phần 3: Quản lý môi trường

### Dừng các máy ảo
```bash
vagrant halt
```

### Khởi động lại các máy ảo
```bash
vagrant up
```

### Xóa môi trường
```bash
vagrant destroy -f
```

## Lưu ý quan trọng
1. Đảm bảo có đủ tài nguyên cho 3 máy ảo (mỗi máy 2GB RAM)
2. Kiểm tra kết nối internet trước khi chạy `vagrant up`
3. Nếu gặp lỗi, có thể xem log chi tiết bằng `vagrant up --debug`

## Kết luận
Môi trường lab này cung cấp:
- 3 máy ảo AlmaLinux 9 đã được cấu hình cơ bản
- Mạng private để các máy có thể giao tiếp với nhau
- Các công cụ cơ bản đã được cài đặt
- SELinux đã được cấu hình phù hợp cho môi trường lab