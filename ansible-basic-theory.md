# Lý Thuyết Cơ Bản về Ansible

## 1. Tổng Quan về Ansible

### 1.1. Ansible là gì?
- Là công cụ tự động hóa mã nguồn mở
- Sử dụng để quản lý cấu hình, triển khai ứng dụng và hệ thống
- Được viết bằng Python
- Sử dụng kiến trúc agentless (không cần cài agent trên các node)

### 1.2. Đặc điểm nổi bật
1. **Không cần agent (Agentless):**
   - Chỉ cần SSH để kết nối đến các node
   - Giảm tải cho hệ thống
   - Dễ dàng triển khai

2. **Dựa trên Python:**
   - Chỉ cần Python trên các node
   - Tận dụng được các module Python
   - Cross-platform

3. **Sử dụng YAML:**
   - Cú pháp đơn giản, dễ đọc
   - Dễ học, dễ viết
   - Định dạng phổ biến

## 2. Kiến Trúc Ansible

### 2.1. Các thành phần chính
1. **Control Node:**
   - Máy cài đặt Ansible
   - Nơi thực thi các lệnh Ansible
   - Yêu cầu: Linux/Unix với Python

2. **Managed Nodes:**
   - Các máy được Ansible quản lý
   - Được kết nối qua SSH
   - Yêu cầu: Python và SSH server

3. **Inventory:**
   - Danh sách các managed nodes
   - Có thể là static hoặc dynamic
   - Định nghĩa các group và biến

4. **Modules:**
   - Các đơn vị thực thi của Ansible
   - Được viết bằng Python
   - Thực hiện các tác vụ cụ thể

### 2.2. Yêu cầu hệ thống
1. **Control Node:**
   ```plaintext
   - Hệ điều hành: Linux/Unix
   - Python 2.7 hoặc Python 3
   - OpenSSH
   - Ansible package
   ```

2. **Managed Nodes:**
   ```plaintext
   - Python 2.7 hoặc Python 3
   - OpenSSH Server
   - Các thư viện Python cần thiết
   ```

## 3. Inventory

### 3.1. Inventory File Cơ bản
```ini
# Basic inventory file: /path/to/inventory
[webservers]
web1 ansible_host=192.168.1.101
web2 ansible_host=192.168.1.102

[dbservers]
db1 ansible_host=192.168.1.201

[all:vars]
ansible_user=ansible
ansible_become=yes
```

### 3.2. Cấu trúc Inventory
1. **Groups:**
   - Nhóm các hosts có chức năng giống nhau
   - Có thể lồng nhau
   - Dễ quản lý và áp dụng cấu hình

2. **Host Variables:**
   ```ini
   web1 ansible_host=192.168.1.101 ansible_port=2222
   ```
   - ansible_host: địa chỉ IP hoặc hostname
   - ansible_port: SSH port
   - ansible_user: user để SSH

3. **Group Variables:**
   ```ini
   [webservers:vars]
   http_port=80
   ```

## 4. Cấu Hình Cơ Bản ansible.cfg

### 4.1. File ansible.cfg
```ini
[defaults]
inventory = /path/to/inventory
remote_user = ansible
host_key_checking = False
private_key_file = ~/.ssh/id_rsa

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```

### 4.2. Các tham số quan trọng
1. **[defaults]:**
   - inventory: đường dẫn đến inventory file
   - remote_user: user mặc định để SSH
   - host_key_checking: tắt kiểm tra host key
   - private_key_file: đường dẫn đến SSH private key

2. **[privilege_escalation]:**
   - become: cho phép privilege escalation
   - become_method: phương thức escalation (sudo)
   - become_user: user đích khi escalate

## 5. Ad-hoc Commands Cơ Bản

### 5.1. Cú pháp cơ bản
```bash
ansible [pattern] -m [module] -a "[arguments]"
```

### 5.2. Module ping
1. **Test kết nối tới tất cả hosts:**
   ```bash
   ansible all -m ping
   ```

2. **Test kết nối tới group cụ thể:**
   ```bash
   ansible webservers -m ping
   ```

3. **Test kết nối với verbose:**
   ```bash
   ansible all -m ping -v
   ```

### 5.3. Các tùy chọn thường dùng
1. **Chỉ định inventory:**
   ```bash
   ansible all -i inventory -m ping
   ```

2. **Chỉ định user:**
   ```bash
   ansible all -u ansible -m ping
   ```

3. **Sử dụng sudo:**
   ```bash
   ansible all -m ping --become
   ```

### 5.4. Kiểm tra kết quả
1. **Kết quả thành công:**
   ```json
   host1 | SUCCESS => {
       "changed": false,
       "ping": "pong"
   }
   ```

2. **Kết quả thất bại:**
   ```json
   host1 | UNREACHABLE! => {
       "changed": false,
       "msg": "Failed to connect to the host",
       "unreachable": true
   }
   ```