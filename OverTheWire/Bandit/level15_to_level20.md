## 🟢 Level 15 -> 16
* **Mục tiêu:** Gửi mật khẩu hiện tại tới cổng 30001 trên localhost, yêu cầu kết nối phải được mã hóa SSL/TLS.
* **Cách giải:**
  1. Đăng nhập vào tài khoản `bandit15`.
  2. Sử dụng công cụ `openssl` với chế độ `s_client` để tạo đường hầm mã hóa tới cổng 30001:
     ```bash
     openssl s_client -connect localhost:30001
     ```
  3. Bỏ qua các thông tin chứng chỉ (Certificate) in ra trên màn hình. Khi đường truyền đã mở, nhập mật khẩu của `bandit15` vào và nhấn Enter để nhận mật khẩu của `bandit16`.
* **Bài học cốt lõi:**
  * **Hạn chế của Netcat (`nc`):** Chỉ truyền tải dữ liệu dạng văn bản thô (plaintext), dễ bị nghe lén và không thể giao tiếp với các dịch vụ yêu cầu bảo mật SSL/TLS.
  * **Sức mạnh của `openssl s_client`:** Được ví như "Netcat có bọc thép". Công cụ này là tiêu chuẩn trong ngành an toàn thông tin để debug, kiểm tra chứng chỉ và tương tác thủ công với các dịch vụ mã hóa như HTTPS, FTPS, hay các cổng SSL tùy chỉnh.
