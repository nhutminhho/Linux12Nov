# Giải thích chi tiết Ansible cho người mới bắt đầu

## 1. Cơ bản về Ansible

### 1.1. Ansible là gì và tại sao nên dùng?

**Ansible là:**
- Công cụ tự động hóa
- Dùng để quản lý nhiều máy từ xa
- Không cần cài đặt phần mềm trên các máy từ xa

**Ví dụ thực tế:**
- Thay vì phải SSH vào 100 máy để cài đặt nginx
- Bạn chỉ cần chạy một lệnh Ansible từ máy của mình
- Ansible sẽ tự động SSH vào 100 máy và cài đặt

### 1.2. Cách Ansible hoạt động

1. **Kết nối:**
   ```plaintext
   Máy bạn (Control Node) --> SSH --> Các máy khác (Managed Nodes)
   ```

2. **Thực thi:**
   - Ansible dùng SSH để kết nối
   - Chạy các lệnh Python trên máy từ xa
   - Trả về kết quả

## 2. Cấu trúc thư mục - Giải thích chi tiết

### 2.1. Tại sao cần tổ chức thư mục?

**Ví dụ không tổ chức thư mục:**
```plaintext
/home/user/
├── inventory1.yml
├── inventory2.yml
├── vars1.yml
├── vars2.yml
├── playbook1.yml
└── playbook2.yml
```

**Vấn đề:**
- Khó tìm file
- Dễ nhầm lẫn
- Khó bảo trì

**Cấu trúc đề xuất:**
```plaintext
ansible-lab/
├── ansible.cfg           # File cấu hình chính
├── inventory/           # Thư mục chứa danh sách máy
│   └── hosts.yml       # File chứa thông tin các máy
├── group_vars/         # Thư mục chứa biến cho nhóm
│   └── all.yml        # Biến chung cho tất cả
├── host_vars/          # Thư mục chứa biến cho từng máy
└── playbooks/          # Thư mục chứa các playbook
```

**Lợi ích:**
1. Dễ tìm kiếm:
   - Cần file inventory? -> Vào thư mục inventory/
   - Cần biến của nhóm? -> Vào group_vars/
   - Cần playbook? -> Vào playbooks/

2. Dễ phân quyền:
   - Có thể giới hạn quyền truy cập từng thư mục
   - Ví dụ: chỉ admin mới được sửa inventory

3. Dễ backup:
   - Backup theo từng phần
   - Không cần backup tất cả

## 3. File Inventory (hosts.yml) - Giải thích chi tiết

### 3.1. Tại sao dùng YAML thay vì INI?

**Format INI:**
```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[dbservers]
db1 ansible_host=192.168.1.20
```

**Format YAML:**
```yaml
all:
  children:
    webservers:
      hosts:
        web1:
          ansible_host: 192.168.1.10
        web2:
          ansible_host: 192.168.1.11
    dbservers:
      hosts:
        db1:
          ansible_host: 192.168.1.20
```

**Lý do chọn YAML:**
1. Cấu trúc rõ ràng hơn:
   - Thấy được cấp bậc qua indent
   - Dễ đọc với file lớn

2. Linh hoạt hơn:
   - Dễ thêm biến phức tạp
   - Dễ mở rộng

### 3.2. Giải thích từng phần trong hosts.yml

```yaml
---
all:                            # Group cao nhất
  children:                     # Khai báo các nhóm con
    nodes:                      # Tên nhóm
      hosts:                    # Danh sách các máy
        node1:                  # Tên máy thứ nhất
          ansible_host: 192.168.56.11    # IP của máy
        node2:                  # Tên máy thứ hai
          ansible_host: 192.168.56.12    # IP của máy
      vars:                     # Biến cho cả nhóm
        ansible_user: ansible   # User SSH
        ansible_become: yes     # Cho phép sudo
        ansible_become_method: sudo   # Dùng sudo
```

**Giải thích chi tiết:**

1. `all:` 
   - Là group mặc định
   - Chứa tất cả các máy
   - Luôn phải có

2. `children:`
   - Dùng để tạo các nhóm con
   - Giúp tổ chức máy theo chức năng
   - Ví dụ: webservers, dbservers

3. `hosts:`
   - Liệt kê các máy trong nhóm
   - Mỗi máy có tên riêng
   - Kèm theo các thông tin cần thiết

4. `ansible_host:`
   - IP hoặc hostname của máy
   - Dùng để SSH vào máy
   - Bắt buộc phải có

5. `vars:`
   - Biến áp dụng cho cả nhóm
   - Không cần khai báo lại cho từng máy
   - Có thể ghi đè nếu cần

## 4. File ansible.cfg - Giải thích chi tiết

### 4.1. Tại sao cần file ansible.cfg?

**Không có ansible.cfg:**
```bash
# Phải gõ nhiều tham số
ansible all -i inventory/hosts.yml \
  --user ansible \
  --private-key ~/.ssh/id_rsa \
  --become \
  -m ping
```

**Có ansible.cfg:**
```bash
# Chỉ cần gõ
ansible all -m ping
```

### 4.2. Giải thích từng option

```ini
[defaults]
# Đường dẫn đến inventory
inventory = ./inventory/hosts.yml
# ↑ Để Ansible biết tìm danh sách máy ở đâu

# User mặc định để SSH
remote_user = ansible
# ↑ User này phải tồn tại trên tất cả các máy

# Tắt kiểm tra SSH host key
host_key_checking = False
# ↑ Tránh bị hỏi xác nhận khi SSH lần đầu

# File SSH private key
private_key_file = ~/.ssh/id_rsa
# ↑ Key này dùng để SSH vào các máy

# Số process chạy đồng thời
forks = 5
# ↑ Càng nhiều càng nhanh nhưng tốn tài nguyên

# Hiện thông tin thay đổi
diff_always = True
# ↑ Để biết Ansible đã thay đổi gì

# File log
log_path = ./ansible.log
# ↑ Ghi lại mọi hoạt động để debug

[privilege_escalation]
# Cho phép dùng sudo
become = True
# ↑ Tự động sudo khi cần

# Dùng sudo để nâng quyền
become_method = sudo
# ↑ Có thể dùng su hoặc pbrun

# Sudo thành user root
become_user = root
# ↑ Có thể đổi thành user khác

# Không hỏi password sudo
become_ask_pass = False
# ↑ Cần cấu hình NOPASSWD trong sudoers
```

## 5. Tips và Lưu ý cho người mới

### 5.1. Kiểm tra cấu hình
```bash
# Kiểm tra inventory
ansible-inventory --list -y

# Kiểm tra biến
ansible all -m debug -a "var=hostvars[inventory_hostname]"

# Test kết nối
ansible all -m ping -v
```

### 5.2. Debug khi gặp lỗi
1. SSH không vào được:
   - Kiểm tra IP trong inventory
   - Kiểm tra user và SSH key
   - Thử SSH thủ công

2. Sudo không chạy:
   - Kiểm tra user có trong sudoers
   - Kiểm tra NOPASSWD đã cấu hình chưa

3. Các lỗi khác:
   - Xem log trong ansible.log
   - Chạy với -vvv để debug
   - Kiểm tra từng bước một