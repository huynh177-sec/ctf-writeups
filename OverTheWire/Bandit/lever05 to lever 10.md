🟢 Level 5 -> 6
 Mật khẩu được giấu trong một file nằm ở đâu đó trong thư mục inhere và nó phải thỏa mãn các thuộc tính sau:
 +) human-readable(chúng ta có thể đọc được)
 +) 1033 bytes in size(kích thước 1033 byte)
 +) not executable(không thể thực thi) : tức là file đó chỉ chứa dữ liệu tĩnh (như văn bản, hình ảnh, âm thanh), không phải là một chương trình phần mềm.
 bash
 ` find . -type f -size 1033c ! -executable`
 `find . -type f -size 1033c ! -executable -exec cat {} \;`

🟢 Level 6 -> 7
 Lần này khác so với các lần trước, mật khẩu được lưu trữ ở đâu đó trên máy chủ và nó thỏa mãn các thuộc tính:
+) owned by user bandit7(thuộc sở hữu của người dùng bandit7)
+) owned by group bandit6(thuộc sở hữu của nhóm bandit6)
+) 33 bytes in size(có kích thước 33 byte)
--> Tóm lại: Tìm một file nằm rải rác đâu đó trên máy chủ (`/`), thuộc sở hữu của user `bandit7`, group `bandit6` và nặng `33 bytes`.
  ```bash
  find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
Trong Linux, mọi câu lệnh đều sinh ra 2 luồng kết quả:

Luồng 1 (Standard Output - stdout): Kết quả thành công (chính là đường dẫn file bạn đang cần).

Luồng 2 (Standard Error - stderr): Các thông báo lỗi (các dòng Permission denied).

Bạn có thể ra lệnh cho Linux: "Hãy lọc toàn bộ các thông báo thuộc Luồng 2, và ném nó vào thùng rác cho tôi, chỉ giữ lại Luồng 1 trên màn hình".
Để làm điều đó, ta gắn thêm cụm 2>/dev/null vào cuối câu lệnh. /dev/null trong Linux được mệnh danh là hố đen, bất cứ thứ gì ném vào đó đều biến mất không dấu vết.
