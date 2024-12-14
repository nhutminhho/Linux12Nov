# Giải Thích Chi Tiết: Script Phân Tích Văn Bản

## 1. Phần Khai Báo và Nhập Liệu

```bash
#!/bin/bash
echo "Nhập đoạn văn bản (nhấn Ctrl+D khi hoàn thành):"
text=$(cat)
```

### Giải thích:
1. **#!/bin/bash**
   - Shebang line: chỉ định shell thực thi script
   - Sử dụng bash vì cần các tính năng của bash shell

2. **echo "Nhập đoạn văn bản..."**
   - Hiển thị hướng dẫn cho người dùng
   - Chỉ rõ cách kết thúc nhập (Ctrl+D)

3. **text=$(cat)**
   - `cat`: đọc input từ bàn phím
   - `$()`: command substitution, lưu kết quả vào biến
   - Input có thể nhiều dòng
   - Ctrl+D để kết thúc nhập

## 2. Đếm Khoảng Cách

```bash
spaces=$(echo "$text" | tr -cd ' ' | wc -c)
```

### Giải thích từng phần:
1. **echo "$text"**
   - In nội dung văn bản
   - Dùng dấu nháy kép để giữ khoảng trắng
   - Output được pipe sang lệnh tiếp theo

2. **tr -cd ' '**
   - `tr`: translate hoặc xóa ký tự
   - `-c`: complement (đảo ngược selection)
   - `-d`: delete các ký tự được chọn
   - `' '`: giữ lại chỉ khoảng trắng
   - Kết quả: xóa tất cả ký tự trừ khoảng trắng

3. **wc -c**
   - `wc`: word count
   - `-c`: đếm số ký tự
   - Đếm số khoảng trắng còn lại

4. **spaces=$(...)**
   - Lưu kết quả đếm vào biến spaces
   - Số khoảng trắng trong văn bản

## 3. Lấy Từ Gần Cuối

```bash
second_last_word=$(echo "$text" | tr -s ' ' '\n' | tail -n 2 | head -n 1)
```

### Giải thích từng lệnh:
1. **echo "$text"**
   - In văn bản để xử lý
   - Dấu nháy kép giữ format

2. **tr -s ' ' '\n'**
   - `-s`: squeeze (gộp các ký tự lặp)
   - `' ' '\n'`: thay khoảng trắng bằng xuống dòng
   - Kết quả: mỗi từ trên một dòng

3. **tail -n 2**
   - Lấy 2 dòng cuối cùng
   - Tức là từ cuối và từ gần cuối

4. **head -n 1**
   - Lấy dòng đầu tiên trong 2 dòng
   - Chính là từ gần cuối

## 4. In Kết Quả

```bash
echo "Số khoảng cách: $spaces"
echo "Từ gần cuối: $second_last_word"
```

### Giải thích:
1. **In số khoảng cách:**
   - Hiển thị kết quả đếm
   - Format dễ đọc

2. **In từ gần cuối:**
   - Hiển thị từ đã tìm được
   - Format rõ ràng

## 5. Ví Dụ Sử Dụng

### Input:
```
Hello world from bash script
```

### Output:
```
Số khoảng cách: 3
Từ gần cuối: from
```

## 6. Lưu Ý Quan Trọng

### 1. Xử Lý Edge Cases:
- Văn bản trống
- Một từ duy nhất
- Nhiều khoảng trắng liên tiếp
- Dấu xuống dòng

### 2. Cải Thiện Script:
```bash
# Kiểm tra input rỗng
if [ -z "$text" ]; then
    echo "Không có input!"
    exit 1
fi

# Chuẩn hóa khoảng trắng
text=$(echo "$text" | tr -s ' ')
```

### 3. Error Handling:
```bash
# Kiểm tra lệnh thực thi thành công
if [ $? -ne 0 ]; then
    echo "Có lỗi xảy ra!"
    exit 1
fi
```

### 4. Performance:
- Với văn bản ngắn: cách hiện tại OK
- Với văn bản dài: cân nhắc dùng awk
- Tránh nhiều pipe nếu có thể

