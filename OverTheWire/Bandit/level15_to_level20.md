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


 
* ## 🟢 Level 16 -> 17
* **Mục tiêu:** Tìm cổng mạng đang mở dịch vụ SSL/TLS trong dải 31000-32000 trên `localhost`, sau đó gửi mật khẩu hiện tại vào cổng đó để thu thập khóa RSA Private Key của level tiếp theo.
* **Cách giải:**
  1. Đăng nhập vào `bandit16`. Sử dụng Nmap để rà quét phiên bản dịch vụ của dải cổng đích (tìm cổng `ssl`, loại trừ các cổng `echo` chim mồi):
     ```bash
     nmap -p 31000-32000 -sV localhost
     ```
     *(Phân tích kết quả: Cổng mục tiêu thực sự là 31790).*
  2. Sử dụng OpenSSL kết nối vào cổng vừa định vị, dùng cờ `-quiet` để bỏ qua các phím tắt nội bộ của công cụ:
     ```bash
     openssl s_client -connect localhost:31790 -quiet
     ```
  3. Dán mật khẩu của `bandit16` vào, copy toàn bộ nội dung khối RSA Private Key trả về.
  4. Thoát khỏi máy chủ (`exit`) để lùi về máy tính cục bộ. Tạo một tệp tin chìa khóa mới và dán khối RSA vào:
     ```bash
     nano sshkey17.private
     ```
  5. Thiết lập quyền bảo mật tuyệt đối cho tệp tin khóa (bắt buộc):
     ```bash
     chmod 600 sshkey17.private
     ```
  6. Xâm nhập vào `bandit17` từ cổng chính bằng chìa khóa vừa tạo và lưu lại mật khẩu phòng hờ:
     ```bash
     ssh -i sshkey17.private bandit17@bandit.labs.overthewire.org -p 2220
     cat /etc/bandit_pass/bandit17
     ```
* **Bài học cốt lõi:**
  * **Lệnh `nmap` (Network Mapper):** Công cụ trinh sát tiêu chuẩn. Cờ `-sV` (Service Version) là chìa khóa để vạch trần bản chất phần mềm thực sự đang chạy ngầm sau một cổng, giúp phân biệt mục tiêu thật và honeypot (bẫy mồi).
  * **Chiến dịch tấn công mạng:** Bài học này mô phỏng trọn vẹn một quy trình thực chiến: Trinh sát hệ thống (Nmap) ➔ Xuyên thủng bảo mật (OpenSSL) ➔ Hậu khai thác và Leo quyền (SSH Key).
  * **Tư duy Không gian (Local vs Remote):** Hệ thống chặn kết nối SSH từ `localhost` là một rào cản kỹ thuật buộc người chơi phải ý thức được vị trí thực thi dòng lệnh: Phải luôn rút lui về Local để đúc chìa khóa, không được làm trên máy chủ mục tiêu.
