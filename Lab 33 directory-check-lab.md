# Lab Thực Hành: Kiểm Tra Thư Mục Trong Bash

## Phần 1: Giải Thích Script

### 1.1 Setup Debug Mode
```bash
#!/bin/bash
set -x

# Giải thích:
# - #!/bin/bash: chỉ định shell thực thi
# - set -x: bật chế độ debug, hiển thị mỗi lệnh trước khi chạy
# - Hữu ích để theo dõi quá trình thực thi script
```

### 1.2 Lấy Thư Mục Hiện Tại
```bash
FOLDER=$(pwd)

# Giải thích:
# - pwd: print working directory
# - $(pwd): lấy kết quả lệnh pwd gán vào biến
# - FOLDER: biến lưu đường dẫn hiện tại
```

## Phần 2: Thực Hành Cơ Bản

### Bài 1: Script Cơ Bản
```bash
#!/bin/bash
set -x

# Thông báo mục đích script
echo "This shell script using for check existing directory"

# Lấy thư mục hiện tại
FOLDER=$(pwd)

# Kiểm tra điều kiện
if [ -d $FOLDER ]; then
    echo "Your input is directory"
    file $FOLDER
elif [ "$FOLDER" = "/path/to/directory" ]; then
    echo "You are in right place"
    ls -la $FOLDER
else
    echo "You are not in right place"
fi

# Giải thích từng phần:
# 1. -d $FOLDER: kiểm tra có phải thư mục không
# 2. file $FOLDER: hiển thị thông tin về thư mục
# 3. ls -la: liệt kê chi tiết nội dung thư mục
```

### Bài 2: Thêm Tính Năng
```bash
#!/bin/bash
set -x

# Thêm timestamp
echo "Script started at: $(date)"

# Lấy và hiển thị thư mục hiện tại
FOLDER=$(pwd)
echo "Current directory: $FOLDER"

# Kiểm tra thư mục
if [ -d "$FOLDER" ]; then
    echo "This is a directory"
    
    # Hiển thị thông tin chi tiết
    echo "Directory information:"
    echo "====================="
    echo "Owner: $(ls -ld "$FOLDER" | awk '{print $3}')"
    echo "Size: $(du -sh "$FOLDER" | cut -f1)"
    echo "Files count: $(ls -1 "$FOLDER" | wc -l)"
    
elif [ "$FOLDER" = "/target/path" ]; then
    echo "Found target directory"
    # Liệt kê nội dung chi tiết
    ls -la "$FOLDER"
    
else
    echo "Not in correct directory"
    echo "Please change to: /target/path"
fi
```

## Phần 3: Bài Tập Thực Hành

### Bài 1: Kiểm Tra Quyền Thư Mục
```bash
#!/bin/bash
set -x

FOLDER=$(pwd)

# Kiểm tra thư mục và quyền
if [ -d "$FOLDER" ]; then
    echo "Directory check:"
    
    # Kiểm tra quyền đọc
    if [ -r "$FOLDER" ]; then
        echo "- Read: YES"
    else
        echo "- Read: NO"
    fi
    
    # Kiểm tra quyền ghi
    if [ -w "$FOLDER" ]; then
        echo "- Write: YES"
    else
        echo "- Write: NO"
    fi
    
    # Kiểm tra quyền thực thi
    if [ -x "$FOLDER" ]; then
        echo "- Execute: YES"
    else
        echo "- Execute: NO"
    fi
else
    echo "Not a directory"
fi

# Giải thích:
# -r: kiểm tra quyền đọc
# -w: kiểm tra quyền ghi
# -x: kiểm tra quyền thực thi
```

### Bài 2: Thống Kê Thư Mục
```bash
#!/bin/bash
set -x

FOLDER=$(pwd)

if [ -d "$FOLDER" ]; then
    echo "Directory Statistics"
    echo "==================="
    
    # Đếm files theo loại
    echo "File types:"
    echo "- Directories: $(find "$FOLDER" -maxdepth 1 -type d | wc -l)"
    echo "- Regular files: $(find "$FOLDER" -maxdepth 1 -type f | wc -l)"
    echo "- Symbolic links: $(find "$FOLDER" -maxdepth 1 -type l | wc -l)"
    
    # Tổng dung lượng
    echo -n "Total size: "
    du -sh "$FOLDER"
fi
```

## Phần 4: Tips và Lưu Ý

### 1. Error Handling
```bash
# Thêm kiểm tra lỗi
if [ $? -ne 0 ]; then
    echo "Error occurred!"
    exit 1
fi
```

### 2. Quoting Variables
```bash
# Luôn đặt biến trong dấu nháy kép
if [ -d "$FOLDER" ]; then
    # Code
fi
```

### 3. Best Practices
1. **Input Validation:**
   ```bash
   # Kiểm tra input
   if [ -z "$FOLDER" ]; then
       echo "Error: No directory specified"
       exit 1
   fi
   ```

2. **Debug Mode:**
   ```bash
   # Bật debug có điều kiện
   if [ "$DEBUG" = "true" ]; then
       set -x
   fi
   ```

3. **Cleanup:**
   ```bash
   # Dọn dẹp khi kết thúc
   trap 'echo "Script ending..."; exit' EXIT
   ```

### 4. Testing
```bash
# Test trong thư mục khác nhau
cd /tmp && ./script.sh
cd /home && ./script.sh
cd /etc && ./script.sh
```

