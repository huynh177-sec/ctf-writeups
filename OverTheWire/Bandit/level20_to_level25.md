## 🟢 Level 20 -> 21 
* **Mục tiêu:** Tương tác với tệp thực thi SUID `suconnect`. Tệp này đóng vai trò là một Client, tự động kết nối đến một cổng mạng được chỉ định và kiểm tra mật khẩu. Nhiệm vụ là phải thiết lập một Server ngầm để đón đầu và cung cấp mật khẩu cũ cho nó.
* **Cách giải:**
  1. Đăng nhập vào `bandit20` và đọc mật khẩu của tài khoản hiện tại để làm "vật tế thần":
     ```bash
     cat /etc/bandit_pass/bandit20
     ```
     *(Giả sử mật khẩu lấy được là: VxCazJaV...)*
  2. Khởi tạo một máy chủ lắng nghe ngầm (Server). Sử dụng `echo` kết hợp đường ống `|` để nạp sẵn mật khẩu, ném vào công cụ `nc` đang mở chốt ở cổng `4444`, và dùng `&` để ẩn toàn bộ tiến trình này xuống nền:
     ```bash
     echo "VxCazJaV..." | nc -l -p 4444 &
     ```
  3. Khởi chạy công cụ (Client) và điều hướng nó lao thẳng vào cổng `4444` vừa mở:
     ```bash
     ./suconnect 4444
     ```
  4. Hệ thống ngầm giao dịch thành công. Thu thập chuỗi mật khẩu của `bandit21` được in ra trên Terminal.

* **Bài học cốt lõi:**
  * **Mô hình Client - Server:** Hiểu được luồng giao tiếp mạng cơ bản. Netcat (`nc -l`) đóng vai trò là Server đứng đợi, `suconnect` đóng vai trò là Client chủ động tìm đến.
  * **Toán tử Pipe (`|`):** Kỹ thuật chuyển hướng đầu ra (Output) của lệnh này làm đầu vào (Input) của lệnh khác. Ở đây là tự động hóa việc "nhét" dữ liệu thay vì gõ tay.
  * **Job Control (`&`):** Kỹ thuật quản lý tiến trình cốt lõi trong Linux. Giúp đẩy một tác vụ xuống chạy ngầm (Background), giải phóng màn hình Terminal để tiếp tục thực thi các câu lệnh khác, giải quyết triệt để bài toán đa nhiệm trên một giao diện đơn.
 


## 🟢 Level 21 -> 22 
* **Mục tiêu:** Thám thính dịch vụ lập lịch tự động `cron` của hệ điều hành. Phân tích luồng thực thi của một cronjob để truy vết vị trí mật khẩu bị rò rỉ.
* **Cách giải:**
  1. Xâm nhập vào thư mục cấu hình lịch trình hệ thống:
     ```bash
     cd /etc/cron.d/
     ls -la
     ```
  2. Đọc bản hợp đồng lập lịch của mục tiêu `bandit22`:
     ```bash
     cat cronjob_bandit22
     ```
     *(Phát hiện cấu hình: Cứ mỗi phút, hệ thống tự động sử dụng đặc quyền của `bandit22` để chạy tệp kịch bản `/usr/bin/cronjob_bandit22.sh`).*
  3. Đọc mã nguồn của tệp kịch bản để xem nó làm gì:
     ```bash
     cat /usr/bin/cronjob_bandit22.sh
     ```
     *(Phân tích mã nguồn: Kịch bản này sao chép mật khẩu từ `/etc/bandit_pass/bandit22` ra một tệp tin tạm tại `/tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv` và thiết lập quyền đọc cho mọi người).*
  4. Thu thập chiến lợi phẩm từ thư mục tạm:
     ```bash
     cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
     ```
  5. Mật khẩu thu thập được:
     * `tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q`

* **Bài học cốt lõi:**
  * **Cấu trúc cấu hình Cron:** Hiểu cú pháp thời gian (`* * * * *`), định danh người dùng thực thi, và đường dẫn kịch bản bên trong `/etc/cron.d/`.
  * **Lỗ hổng Logic (Logic Flaw):** Lỗ hổng không nằm ở việc sai cú pháp lệnh, mà nằm ở quy trình luân chuyển dữ liệu bất cẩn (Mang dữ liệu nhạy cảm ra thư mục công cộng `/tmp/` và cấp quyền `644`).
  * **Cronjob Privilege Escalation:** Kỹ thuật lợi dụng các công việc tự động chạy dưới quyền cao hơn để trích xuất thông tin hoặc leo thang đặc quyền.
