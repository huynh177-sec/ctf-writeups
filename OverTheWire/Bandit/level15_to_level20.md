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



     ## 🟢 Level 17 -> 18
* **Mục tiêu:** Tìm mật khẩu của `bandit18` được giấu dưới dạng dòng văn bản bị thay đổi duy nhất giữa hai tệp tin `passwords.old` và `passwords.new` nằm trong thư mục gốc.
* **Cách giải:**
  1. Đăng nhập vào máy chủ bằng tài khoản `bandit17`.
     *(Tại đây, sử dụng thói quen liệt kê thư mục `ls -la` để phát hiện vị trí của hai tệp tin mục tiêu).*
  2. Không sử dụng `cat` vì dữ liệu quá lớn (3300 bytes), tiến hành chạy công cụ đối chiếu để tìm điểm dị biệt:
     ```bash
     diff passwords.old passwords.new
     ```
  3. Phân tích kết quả trả về từ công cụ đối chiếu. Mật khẩu cần tìm là chuỗi văn bản nằm ở hàng có dấu `>` (biểu thị dữ liệu mới được thêm vào ở tệp `passwords.new`).
     * `kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd`

* **Bài học cốt lõi:**
  * **Lệnh `diff` (Difference):** Công cụ cốt lõi trong Linux dùng để so sánh toàn diện hai tệp tin. Dấu `<` chỉ dữ liệu cũ, dấu `>` chỉ dữ liệu mới.
  * **Ứng dụng thực tiễn:** Trong hoạt động phân tích pháp y kỹ thuật số (Digital Forensics) và kiểm thử xâm nhập (Pentest), việc dò tìm bằng mắt thường là bất khả thi. Lệnh `diff` là vũ khí tiêu chuẩn để kiểm tra tính toàn vẹn của dữ liệu (Data Integrity Check), giúp dò ra những dòng mã độc hoặc cấu hình đã bị hacker âm thầm thay đổi.
  * **Lưu ý chiến thuật:** Đề bài đã cảnh báo trước về cơ chế bẫy "Byebye!" của Level 18. Máy chủ sẽ tự động ngắt kết nối ngay khi đăng nhập. Đây là tín hiệu báo trước bài học tiếp theo sẽ liên quan đến kỹ thuật vượt ngục môi trường shell (Escape Shell).
* **Bài học cốt lõi:**
  * **Lệnh `nmap` (Network Mapper):** Công cụ trinh sát tiêu chuẩn. Cờ `-sV` (Service Version) là chìa khóa để vạch trần bản chất phần mềm thực sự đang chạy ngầm sau một cổng, giúp phân biệt mục tiêu thật và honeypot (bẫy mồi).
  * **Chiến dịch tấn công mạng:** Bài học này mô phỏng trọn vẹn một quy trình thực chiến: Trinh sát hệ thống (Nmap) ➔ Xuyên thủng bảo mật (OpenSSL) ➔ Hậu khai thác và Leo quyền (SSH Key).
  * **Tư duy Không gian (Local vs Remote):** Hệ thống chặn kết nối SSH từ `localhost` là một rào cản kỹ thuật buộc người chơi phải ý thức được vị trí thực thi dòng lệnh: Phải luôn rút lui về Local để đúc chìa khóa, không được làm trên máy chủ mục tiêu.
 

## 🟢 Level 19 -> 20 (Day 18)
* **Mục tiêu:** Vượt qua rào cản phân quyền của hệ thống bằng cách lợi dụng một tệp tin thực thi nội bộ để đọc mật khẩu của tài khoản cấp cao hơn (`bandit20`).
* **Cách giải:**
  1. Đăng nhập vào tài khoản `bandit19` bằng giao thức SSH.
  2. Rà soát môi trường hiện tại bằng lệnh liệt kê chi tiết:
     ```bash
     ls -la
     ```
     *(Phát hiện tệp tin `bandit20-do` thuộc sở hữu của `bandit20`, đặc biệt được gắn cờ SUID `s` ở nhóm quyền thực thi).*
  3. Kiểm tra cách hoạt động của công cụ bằng cách gọi nó trong thư mục hiện tại (sử dụng dấu `./`):
     ```bash
     ./bandit20-do
     ```
     *(Hệ thống trả về hướng dẫn: Công cụ này sẽ chạy bất kỳ câu lệnh nào được truyền vào dưới tư cách của một người dùng khác).*
  4. Thực thi đòn khai thác (Exploit). Giao cho công cụ này nhiệm vụ đọc file mật khẩu. Nó sẽ tự động "đội lốt" `bandit20` để vượt qua kiểm tra an ninh:
     ```bash
     ./bandit20-do cat /etc/bandit_pass/bandit20
     ```
  5. Thu thập mật khẩu in ra trên màn hình và lưu trữ để tiến vào Level 20.

* **Bài học cốt lõi:**
  * **Đặc quyền SUID (Set owner User ID):** Cơ chế ma thuật của Linux. Khi một file có cờ `s` được chạy, tiến trình đó sẽ mang quyền hạn của **người tạo ra file** thay vì **người bấm nút chạy**. 
  * **Leo thang đặc quyền (Privilege Escalation):** Trong thực chiến Pentest hoặc các giải đấu CTF (mảng Pwn/Boot2Root), việc săn lùng các file có cờ SUID cấu hình lỏng lẻo là kỹ thuật kinh điển để Hacker từ một tài khoản "khách" leo lên chiếm quyền "Quản trị viên" (Root).
  * **Cú pháp `./`:** Phân biệt rõ lệnh toàn cục (như `cat`, `ls`) và tệp thực thi nội bộ. Ký hiệu `./` chỉ định hệ điều hành chạy chính xác file đang nằm trong thư mục hiện tại.
