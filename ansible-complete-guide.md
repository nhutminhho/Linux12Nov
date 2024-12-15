# Ansible - Hướng dẫn cho người mới bắt đầu

## Phần 1: Lý thuyết cơ bản

### 1. Ansible là gì?
- Công cụ tự động hóa IT, quản lý cấu hình
- Không cần cài đặt agent (agentless)
- Sử dụng SSH để kết nối
- Dùng YAML để viết cấu hình

### 2. Thành phần cơ bản
1. **Control Node:**
   - Máy cài đặt Ansible
   - Nơi chạy các lệnh ansible
   - Yêu cầu: Linux với Python

2. **Managed Node:**
   - Các máy được Ansible quản lý
   - Yêu cầu: Python và SSH server

3. **Inventory:**
   - File chứa thông tin các máy quản lý
   - Có thể viết dạng INI hoặc YAML

4. **Configuration file:**
   - File cấu hình của Ansible
   - Tên mặc định: ansible.cfg

## Phần 2: Cài đặt và Cấu hình

### 1. Cài đặt Ansible trên Control Node
```bash
# Trên RHEL/CentOS/Fedora
sudo dnf install epel-release
sudo dnf install ansible

# Trên Ubuntu/Debian
sudo apt update
sudo apt install ansible
```

### 2. Cấu trúc thư mục làm việc
```bash
mkdir -p ~/ansible-lab
cd ~/ansible-lab
mkdir inventory
mkdir group_vars
mkdir host_vars
mkdir playbooks
```

Cấu trúc thư mục:
```plaintext
ansible-lab/
├── ansible.cfg
├── inventory/
│   └── hosts.yml
├── group_vars/
│   └── all.yml
├── host_vars/
└── playbooks/
```

### 3. File Inventory (hosts.yml)
```yaml
# ~/ansible-lab/inventory/hosts.yml
---
all:
  children:
    nodes:
      hosts:
        node1:
          ansible_host: 192.168.56.11
        node2:
          ansible_host: 192.168.56.12
      vars:
        ansible_user: ansible
        ansible_become: yes
        ansible_become_method: sudo
```

### 4. File Configuration (ansible.cfg)
```ini
# ~/ansible-lab/ansible.cfg
[defaults]
# Đường dẫn tới inventory
inventory = ./inventory/hosts.yml

# User mặc định để SSH
remote_user = ansible

# Không kiểm tra SSH host key
host_key_checking = False

# Đường dẫn tới SSH private key
private_key_file = ~/.ssh/id_rsa

# Số lượng process chạy song song
forks = 5

# Hiển thị diff khi thay đổi file
diff_always = True

# File log
log_path = ./ansible.log

[privilege_escalation]
# Sử dụng sudo
become = True
become_method = sudo
become_user = root
become_ask_pass = False
```

## Phần 3: Bài Lab Thực Hành

### Lab 1: Kiểm tra kết nối cơ bản

#### Bước 1: Chuẩn bị môi trường
```bash
# Tạo thư mục làm việc
mkdir -p ~/ansible-lab
cd ~/ansible-lab

# Tạo các thư mục cần thiết
mkdir -p {inventory,group_vars,host_vars,playbooks}
```

#### Bước 2: Tạo file inventory
```bash
# Tạo file inventory
cat << 'EOF' > inventory/hosts.yml
---
all:
  children:
    nodes:
      hosts:
        node1:
          ansible_host: 192.168.56.11
        node2:
          ansible_host: 192.168.56.12
      vars:
        ansible_user: ansible
        ansible_become: yes
        ansible_become_method: sudo
EOF
```

#### Bước 3: Tạo file ansible.cfg
```bash
# Tạo file ansible.cfg
cat << 'EOF' > ansible.cfg
[defaults]
inventory = ./inventory/hosts.yml
remote_user = ansible
host_key_checking = False
private_key_file = ~/.ssh/id_rsa
forks = 5
diff_always = True
log_path = ./ansible.log

[privilege_escalation]
become = True
become_method = sudo
become_user = root
become_ask_pass = False
EOF
```

#### Bước 4: Kiểm tra cấu hình
```bash
# Kiểm tra inventory
ansible-inventory --list -y

# Kiểm tra config
ansible --version
```

#### Bước 5: Test kết nối
```bash
# Ping tất cả hosts
ansible all -m ping

# Ping group nodes
ansible nodes -m ping

# Ping host cụ thể
ansible node1 -m ping
```

### Lab 2: Thu thập thông tin hệ thống

#### Bước 1: Kiểm tra thông tin hệ thống
```bash
# Thu thập tất cả thông tin
ansible all -m setup

# Thu thập thông tin OS
ansible all -m setup -a "filter=ansible_distribution*"

# Thu thập thông tin memory
ansible all -m setup -a "filter=ansible_memory_mb"
```

#### Bước 2: Lưu thông tin vào file
```bash
# Tạo thư mục lưu output
mkdir -p outputs

# Lưu thông tin vào file
ansible all -m setup > outputs/system_info.txt
```

## Phần 4: Các lệnh ad-hoc thường dùng

### 1. Kiểm tra kết nối
```bash
# Ping test
ansible all -m ping

# Với verbose
ansible all -m ping -v
```

### 2. Thực thi lệnh
```bash
# Xem hostname
ansible all -m command -a "hostname"

# Kiểm tra uptime
ansible all -m command -a "uptime"
```

### 3. Kiểm tra tài nguyên
```bash
# Kiểm tra disk space
ansible all -m shell -a "df -h"

# Kiểm tra memory
ansible all -m shell -a "free -m"
```

## Phần 5: Xử lý lỗi thường gặp

### 1. Lỗi SSH
- Kiểm tra SSH key có đúng permissions (600)
- Kiểm tra user có quyền SSH
- Kiểm tra host key checking

### 2. Lỗi sudo
- Kiểm tra user có trong group sudo/wheel
- Kiểm tra NOPASSWD trong sudoers
- Kiểm tra become_user trong ansible.cfg

### 3. Debug
```bash
# Chạy với verbose level khác nhau
ansible all -m ping -v     # basic
ansible all -m ping -vv    # more
ansible all -m ping -vvv   # debug
ansible all -m ping -vvvv  # connection debug
```