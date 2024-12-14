# Hướng dẫn chi tiết giải bài tập Quản lý User Linux

## I. Phân tích yêu cầu của đề bài

### 1. Tạo cấu trúc thư mục
```plaintext
/home/
├── banGiamDoc/   # Thư mục cho Ban Giám đốc
├── keToan/       # Thư mục cho phòng Kế toán
├── kinhDoanh/    # Thư mục cho phòng Kinh doanh
├── kyThuat/      # Thư mục cho phòng Kỹ thuật
├── nhanSu/       # Thư mục cho phòng Nhân sự
└── cntt/         # Thư mục cho phòng IT
```

### 2. Yêu cầu về user
- Tên user theo cột USER trong bảng
- Thêm description đầy đủ họ tên
- Ghi chú chức vụ cho giám đốc, phó giám đốc

### 3. Yêu cầu về thư mục cá nhân
- Nằm trong thư mục phòng ban tương ứng
- Có file quydinhcongty.txt
- Có 3 thư mục con: duLieu, giaiTri, luuTru

### 4. Yêu cầu về phân quyền sudo
- Trưởng phòng IT: full quyền root
- Trưởng phòng nhân sự: quyền tạo user và đặt password

## II. Giải quyết chi tiết

### A. Tạo cấu trúc thư mục chính

```bash
#!/bin/bash

# Tạo các thư mục phòng ban
echo "=== Tạo cấu trúc thư mục chính ==="
for dept in banGiamDoc keToan kinhDoanh kyThuat nhanSu cntt
do
    sudo mkdir -p /home/$dept
    sudo chmod 755 /home/$dept
    echo "Đã tạo: /home/$dept"
done
```

**Giải thích:**
- Sử dụng vòng lặp for để tạo thư mục nhanh chóng
- mkdir -p để không báo lỗi nếu thư mục đã tồn tại
- chmod 755 để:
  - Owner có full quyền (7)
  - Group có quyền đọc và thực thi (5)
  - Others có quyền đọc và thực thi (5)

### B. Tạo các Groups

```bash
#!/bin/bash

echo "=== Tạo các groups ==="
# Danh sách groups cần tạo
declare -a groups=(
    "giamdoc"
    "ketoan"
    "kinhdoanh"
    "kythuat"
    "nhansu"
    "cntt"
)

# Tạo từng group
for group in "${groups[@]}"
do
    sudo groupadd $group 2>/dev/null || echo "Group $group đã tồn tại"
    echo "Đã tạo group: $group"
done
```

**Giải thích:**
- Sử dụng array để quản lý danh sách groups
- 2>/dev/null để loại bỏ thông báo lỗi nếu group đã tồn tại
- Mỗi group tương ứng với một phòng ban

### C. Function tạo User

```bash
#!/bin/bash

create_user() {
    local username=$1
    local fullname=$2
    local group=$3
    local dept=$4
    local position=$5

    # 1. Tạo description
    local description="$fullname"
    if [ "$position" = "Giám đốc" ]; then
        description="$fullname - Giám đốc"
    elif [ "$position" = "Phó giám đốc" ]; then
        description="$fullname - Phó giám đốc"
    fi

    # 2. Tạo user
    sudo useradd -m \
        -d "/home/$dept/$username" \
        -g $group \
        -c "$description" \
        -s /bin/bash \
        "$username"

    # 3. Tạo cấu trúc thư mục
    local home_dir="/home/$dept/$username"
    sudo mkdir -p "$home_dir"/{duLieu,giaiTri,luuTru}
    sudo touch "$home_dir/quydinhcongty.txt"

    # 4. Set permissions
    sudo chown -R "$username:$group" "$home_dir"
    sudo chmod 700 "$home_dir"

    echo "Đã tạo user: $username"
}
```

**Giải thích từng phần:**
1. **Tham số function:**
   - username: Tên đăng nhập
   - fullname: Họ tên đầy đủ
   - group: Group của user
   - dept: Thư mục phòng ban
   - position: Chức vụ

2. **Options useradd:**
   - -m: Tạo thư mục home
   - -d: Chỉ định đường dẫn home
   - -g: Chỉ định primary group
   - -c: Thêm comment/description
   - -s: Chỉ định shell

3. **Cấu trúc thư mục:**
   - mkdir -p để tạo nhiều thư mục con
   - touch để tạo file quy định
   - chown để set ownership
   - chmod 700 để chỉ owner có quyền truy cập

### D. Tạo User theo danh sách

```bash
#!/bin/bash

# Ví dụ tạo một số user đại diện
echo "=== Tạo users ==="

# 1. Giám đốc
create_user "sy.tt" "TRƯƠNG TIẾN SỸ" "giamdoc" "banGiamDoc" "Giám đốc"

# 2. Phó giám đốc
create_user "khanh.tv" "TRẦN VĂN KHÁNH" "giamdoc" "banGiamDoc" "Phó giám đốc"

# 3. Trưởng phòng IT
create_user "huy.nq" "NGUYỄN QUỐC HUY" "cntt" "cntt" "Trưởng phòng"

# 4. Trưởng phòng nhân sự
create_user "yen.dv" "ĐỖ VĂN YÊN" "nhansu" "nhanSu" "Trưởng phòng"
```

### E. Thiết lập quyền sudo

```bash
#!/bin/bash

echo "=== Thiết lập quyền sudo ==="

# 1. Trưởng phòng IT - Full quyền
sudo tee /etc/sudoers.d/it_manager << EOF
huy.nq ALL=(ALL) NOPASSWD: ALL
EOF
sudo chmod 440 /etc/sudoers.d/it_manager

# 2. Trưởng phòng nhân sự - Quyền hạn chế
sudo tee /etc/sudoers.d/hr_manager << EOF
yen.dv ALL=(ALL) NOPASSWD: /usr/sbin/useradd, /usr/bin/passwd
EOF
sudo chmod 440 /etc/sudoers.d/hr_manager
```

**Giải thích:**
- Sử dụng /etc/sudoers.d/ thay vì sửa trực tiếp /etc/sudoers
- NOPASSWD để không cần nhập password khi sudo
- chmod 440 để đảm bảo an toàn cho file sudoers

### F. Kiểm tra kết quả

```bash
# 1. Kiểm tra thư mục
ls -l /home/

# 2. Kiểm tra users
id sy.tt
id huy.nq

# 3. Kiểm tra cấu trúc thư mục user
ls -la /home/cntt/huy.nq/

# 4. Kiểm tra quyền sudo
sudo -l -U huy.nq
sudo -l -U yen.dv
```

## III. Script hoàn chỉnh

```bash
#!/bin/bash

# Colors for output
GREEN='\033[0;32m'
NC='\033[0m'

echo -e "${GREEN}=== Bắt đầu setup hệ thống ===${NC}"

# 1. Tạo thư mục
create_directories() {
    for dept in banGiamDoc keToan kinhDoanh kyThuat nhanSu cntt; do
        mkdir -p /home/$dept
        chmod 755 /home/$dept
    done
}

# 2. Tạo groups
create_groups() {
    for group in giamdoc ketoan kinhdoanh kythuat nhansu cntt; do
        groupadd $group 2>/dev/null || echo "Group $group đã tồn tại"
    done
}

# 3. Function tạo user
create_user() {
    local username=$1
    local fullname=$2
    local group=$3
    local dept=$4
    local position=$5

    local description="$fullname"
    [ "$position" = "Giám đốc" ] && description="$fullname - Giám đốc"
    [ "$position" = "Phó giám đốc" ] && description="$fullname - Phó giám đốc"

    useradd -m -d "/home/$dept/$username" -g $group -c "$description" -s /bin/bash "$username"
    
    local home_dir="/home/$dept/$username"
    mkdir -p "$home_dir"/{duLieu,giaiTri,luuTru}
    touch "$home_dir/quydinhcongty.txt"
    
    chown -R "$username:$group" "$home_dir"
    chmod 700 "$home_dir"
}

# 4. Setup sudo
setup_sudo() {
    # IT Manager
    echo "huy.nq ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/it_manager
    chmod 440 /etc/sudoers.d/it_manager

    # HR Manager
    echo "yen.dv ALL=(ALL) NOPASSWD: /usr/sbin/useradd, /usr/bin/passwd" > /etc/sudoers.d/hr_manager
    chmod 440 /etc/sudoers.d/hr_manager
}

# Main execution
echo "Creating directories..."
create_directories

echo "Creating groups..."
create_groups

echo "Creating users..."
create_user "sy.tt" "TRƯƠNG TIẾN SỸ" "giamdoc" "banGiamDoc" "Giám đốc"
create_user "khanh.tv" "TRẦN VĂN KHÁNH" "giamdoc" "banGiamDoc" "Phó giám đốc"
create_user "huy.nq" "NGUYỄN QUỐC HUY" "cntt" "cntt" "Trưởng phòng"
create_user "yen.dv" "ĐỖ VĂN YÊN" "nhansu" "nhanSu" "Trưởng phòng"

echo "Setting up sudo privileges..."
setup_sudo

echo -e "${GREEN}=== Setup hoàn tất ===${NC}"
```

## IV. Chú ý quan trọng

1. **Thứ tự thực hiện:**
   - Tạo thư mục trước
   - Tạo group trước user
   - Setup sudo cuối cùng

2. **Bảo mật:**
   - Phân quyền chính xác cho thư mục
   - Sudo chỉ cho người được chỉ định
   - File sudoers đúng permission

3. **Kiểm tra:**
   - Verify sau mỗi bước
   - Test quyền sudo
   - Kiểm tra cấu trúc thư mục

---

*Note: Script này dành cho mục đích học tập. Trong môi trường thực tế cần thêm các biện pháp bảo mật khác.*