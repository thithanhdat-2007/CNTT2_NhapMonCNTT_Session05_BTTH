# Bài tập tổng hợp: Giao diện dòng lệnh Terminal cơ bản

Họ và tên: Thi Thành Đạt  
MSSV: N26DTCN041  
Lớp: CNTT2  
Email: thithanhdat111@gmail.com

## Phần 1. CLI, GUI và cấu trúc câu lệnh

**1. Phân tích câu lệnh `ls -Force C:\Windows\Logs`:**

- Command (lệnh): `ls`, là tên viết ngắn của `Get-ChildItem`, dùng để liệt kê nội dung thư mục.
- Option/Flag (tùy chọn): `-Force`, cho phép hiển thị cả các mục ẩn.
- Argument (đối số): `C:\Windows\Logs`, là thư mục cần xem.

**2. Vì sao cần Terminal khi làm việc với máy chủ Cloud?**

CLI là giao diện nhập lệnh bằng chữ; GUI là giao diện có cửa sổ và nút bấm. Máy chủ Cloud có thể không cài giao diện đồ họa. Nhóm có thể kết nối từ xa bằng SSH rồi dùng Terminal để tạo thư mục, kiểm tra file và chạy chương trình. Vì vậy, biết dùng Terminal giúp nhóm thao tác được trên máy chủ ngay cả khi không có GUI.

## Phần 2. Điều hướng hệ thống file

**1. Từ `C:\Users\student\Desktop` đi vào thư mục dự án bằng đường dẫn tương đối:**

```powershell
cd .\quan-ly-sinh-vien
```

Dấu `.` chỉ thư mục đang đứng.

**2. Đường dẫn tuyệt đối đến `main.py`:**

```text
C:\Users\student\Desktop\quan-ly-sinh-vien\src\main.py
```

Đường dẫn tuyệt đối ghi đầy đủ từ ổ đĩa đến file, nên không phụ thuộc vào vị trí hiện tại của Terminal.

**3. Đứng trong `src`, xem cả file ẩn của thư mục `data` bằng một lệnh:**

```powershell
ls -Force ..\data
```

`..` đi lên thư mục cha của `src`, sau đó vào thư mục `data` cùng cấp.

## Phần 3. Thao tác file và thư mục

Các lệnh sau được viết với giả định PowerShell đang ở `C:\Users\student\Desktop`. PowerShell có thể tạo luôn thư mục cha `quan-ly-sinh-vien` khi tạo hai thư mục con; không dùng cờ `-p` của Bash.

**1. Tạo `src` và `data` trong một lệnh:**

```powershell
New-Item -ItemType Directory -Path .\quan-ly-sinh-vien\src, .\quan-ly-sinh-vien\data
```

**2. Tạo ba file rỗng trong một lệnh:**

```powershell
New-Item -ItemType File -Path .\quan-ly-sinh-vien\src\main.py, .\quan-ly-sinh-vien\src\utils.py, .\quan-ly-sinh-vien\README.md
```

Để cấu trúc dự án khớp hoàn toàn với đề, tạo thêm `input.csv` trong thư mục `data`:

```powershell
New-Item -ItemType File -Path .\quan-ly-sinh-vien\data\input.csv
```

**3. Nếu lỡ tạo thư mục gốc tên `quan-ly-sv`, đổi tên như sau:**

```powershell
Rename-Item -Path .\quan-ly-sv -NewName quan-ly-sinh-vien
```

Đây là cách sửa trong trường hợp đặt sai tên; không chạy lệnh này nếu thư mục đã có tên đúng.

**4. Sao lưu cả thư mục dự án và các file bên trong:**

```powershell
Copy-Item -Path .\quan-ly-sinh-vien -Destination .\backup-quan-ly-sinh-vien -Recurse
```

`-Recurse` có nghĩa là sao chép cả các thư mục và file con.

## Phần 4. Chẩn đoán lỗi Terminal

**1. Log số 2: `cd scr`**

Nguyên nhân có khả năng nhất là gõ nhầm `scr` thay vì `src`, nên PowerShell không tìm thấy thư mục. Đứng ở thư mục gốc dự án, gõ `cd .\s` rồi nhấn `Tab`: PowerShell sẽ gợi ý tên thư mục đang có, chẳng hạn `src`. Có thể kiểm tra thêm bằng `ls`, sau đó dùng lệnh `cd .\src`.

**2. Log số 3: `Access is denied` khi chạy `setup.ps1`**

Đây là lỗi bị từ chối quyền truy cập trên Windows. Trước tiên kiểm tra file có tồn tại và quyền đối với file:

```powershell
Test-Path .\setup.ps1
Get-Acl .\setup.ps1 | Format-List
```

Nếu tài khoản không có quyền đọc hoặc chạy file, cần nhờ người quản lý cấp quyền. Nếu file nằm trong vị trí được bảo vệ và em được phép dùng quyền quản trị, có thể mở PowerShell với quyền quản trị bằng `Start-Process powershell -Verb RunAs`, chuyển đến đúng thư mục rồi chạy lại `.\setup.ps1`. Không nên tự tắt cơ chế bảo vệ. Cũng cần phân biệt với lỗi do Execution Policy: lỗi đó thường nói rõ script không được tải vì việc chạy script bị vô hiệu hóa. Nếu thông báo chi tiết cho thấy đúng là lỗi Execution Policy, kiểm tra bằng `Get-ExecutionPolicy -List`, rồi có thể đặt chính sách cho tài khoản hiện tại bằng `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned` theo hướng dẫn quản trị, sau đó chạy lại `.\setup.ps1`.

**3. Log số 1: cảnh báo khi xóa `old-version`**

Thư mục còn chứa file hoặc thư mục con nên PowerShell hỏi để tránh xóa nhầm. Sau khi kiểm tra đúng thư mục cũ cần xóa, dùng:

```powershell
Remove-Item -Path .\old-version -Recurse -Force
```

`-Recurse` xóa cả nội dung bên trong; `-Force` giúp thực hiện không cần hỏi xác nhận về nội dung con.

**4. Log số 4: không tìm thấy `data/input.csv`**

Em đọc lỗi theo ba bước:

| Bước | Câu trả lời |
|---|---|
| (a) Đọc dòng cuối để thấy lỗi trực tiếp | `FileNotFoundError: [Errno 2] No such file or directory: 'data/input.csv'` |
| (b) Bắt từ khóa lỗi | `FileNotFoundError` và `No such file or directory` cho biết chương trình không tìm thấy file ở đường dẫn đã dùng. |
| (c) Kiểm tra ngữ cảnh | Terminal đang đứng trong `src`, trong khi `data` là thư mục cùng cấp với `src`. Vì vậy `data/input.csv` bị hiểu là `src/data/input.csv`, không phải file thật trong thư mục dự án. Kiểm tra bằng `pwd` và `ls -Force ..\data`. |

Một cách chạy đúng khi chương trình dùng đường dẫn tương đối `data/input.csv` là quay về thư mục gốc dự án, rồi chạy file Python:

```powershell
cd ..
python .\src\main.py
```

Khi đó `data/input.csv` được tính từ thư mục gốc dự án. Nếu file chưa tồn tại thì tạo file đó trước; thao tác đổi thư mục một mình không thể thay thế file bị thiếu.

