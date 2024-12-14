# Thực Hành Cấu Hình Shell Startup Files

## Mục Tiêu
- Hiểu về các file khởi động của shell
- Thực hành cấu hình system-wide và user-specific
- Tùy chỉnh môi trường shell cho người dùng

## Chuẩn Bị
- Hệ thống Linux (AlmaLinux/CentOS)
- Quyền root hoặc sudo
- Terminal để thực hành

## Lab 1: Khám Phá Shell Startup Files

### Bước 1: Xem các file hệ thống
```bash
# Đăng nhập với quyền root
sudo -i

# Xem nội dung /etc/profile
cat /etc/profile

# Xem các script trong /etc/profile.d
ls -l /etc/profile.d/
cat /etc/profile.d/bash_completion.sh

# Xem nội dung /etc/bashrc
cat /etc/bashrc
```

### Bước 2: Tạo user test
```bash
# Tạo user mới để thực hành
useradd -m testuser
passwd testuser
# Đặt mật khẩu: Test123@

# Kiểm tra các file trong home directory
ls -la /home/testuser/
```

## Lab 2: Tùy Chỉnh System-wide Settings

### Bước 1: Tạo script trong /etc/profile.d
```bash
# Tạo script custom.sh
cat > /etc/profile.d/custom.sh << 'EOF'
# Custom system-wide settings
export CUSTOM_PATH="/opt/custom/bin"
export CUSTOM_ENV="production"

# Custom alias
alias ll='ls -la'
alias df='df -h'

# Custom prompt
PS1='\[\e[1;32m\][\u@\h \W]\$\[\e[0m\] '
EOF

# Đặt quyền thực thi
chmod 755 /etc/profile.d/custom.sh
```

### Bước 2: Kiểm tra hiệu lực
```bash
# Đăng xuất và đăng nhập lại
exit
sudo -i

# Kiểm tra biến môi trường
echo $CUSTOM_PATH
echo $CUSTOM_ENV

# Thử alias
ll
```

## Lab 3: Tùy Chỉnh User-specific Settings

### Bước 1: Cấu hình .bashrc cho testuser
```bash
# Chuyển sang user testuser
su - testuser

# Chỉnh sửa .bashrc
cat >> ~/.bashrc << 'EOF'

# User specific aliases
alias projects='cd ~/projects'
alias notes='nano ~/notes.txt'

# Custom functions
function mkcd() {
    mkdir -p "$1" && cd "$1"
}

# Custom prompt
PS1='\[\e[1;36m\][\u@\h \W]\$\[\e[0m\] '
EOF
```

### Bước 2: Cấu hình .bash_profile
```bash
# Chỉnh sửa .bash_profile
cat >> ~/.bash_profile << 'EOF'

# User specific environment
export PATH="$HOME/bin:$PATH"
export EDITOR="nano"
export VISUAL="nano"

# User specific history settings
export HISTSIZE=2000
export HISTFILESIZE=2000
export HISTTIMEFORMAT="%F %T "
EOF
```

### Bước 3: Áp dụng thay đổi
```bash
# Tải lại cấu hình
source ~/.bashrc
source ~/.bash_profile

# Kiểm tra
echo $EDITOR
echo $HISTSIZE
```

## Lab 4: Tạo Custom Environment

### Bước 1: Tạo cấu trúc thư mục
```bash
# Tạo thư mục projects
mkdir ~/projects

# Tạo các thư mục con
mkcd ~/projects/website
mkcd ~/projects/scripts

# Kiểm tra alias mới
projects
```

### Bước 2: Thử nghiệm function
```bash
# Sử dụng function mkcd mới
mkcd ~/projects/newproject

# Kiểm tra vị trí hiện tại
pwd
```

## Bài Tập Thực Hành

### Bài tập 1: Tạo System-wide Script
1. Tạo file `/etc/profile.d/development.sh`
2. Thêm các biến môi trường cho development
3. Thêm alias hữu ích cho developers
4. Kiểm tra với user mới

```bash
# Mẫu script
sudo cat > /etc/profile.d/development.sh << 'EOF'
# Development environment
export DEV_HOME="/opt/development"
export JAVA_HOME="/usr/lib/jvm/java-11"
export MAVEN_HOME="/opt/maven"
export PATH="$JAVA_HOME/bin:$MAVEN_HOME/bin:$PATH"

# Development aliases
alias mvn-clean='mvn clean install'
alias git-status='git status'
alias docker-ps='docker ps --format "table {{.Names}}\t{{.Status}}"'
EOF

sudo chmod 755 /etc/profile.d/development.sh
```

### Bài tập 2: Tùy Chỉnh User Environment
1. Tạo user developer
2. Cấu hình .bashrc với các alias cho coding
3. Thêm các function hữu ích
4. Tùy chỉnh prompt hiển thị git branch

## Xử Lý Lỗi Thường Gặp

### 1. Syntax Error trong Scripts
```bash
# Kiểm tra syntax
bash -n ~/.bashrc

# Debug script
bash -x ~/.bashrc
```

### 2. Path Không Hoạt Động
```bash
# Kiểm tra PATH
echo $PATH

# Kiểm tra thứ tự load
cat ~/.bash_profile
cat ~/.bashrc
```

### 3. Alias Không Hoạt Động
```bash
# Kiểm tra alias đã định nghĩa
alias

# Tải lại cấu hình
source ~/.bashrc
```

## Ghi Chú Quan Trọng

1. **Thứ tự Load File**
   - /etc/profile
   - /etc/profile.d/*.sh
   - ~/.bash_profile
   - ~/.bashrc
   - /etc/bashrc

2. **Best Practices**
   - Backup trước khi sửa
   - Sử dụng comment giải thích
   - Test trên user test trước
   - Kiểm tra syntax trước khi áp dụng

3. **Tips Hữu Ích**
   - Sử dụng `source` để áp dụng thay đổi
   - Kiểm tra với `echo` trước khi export
   - Dùng `alias` để xem danh sách alias
   - Backup file cấu hình quan trọng

