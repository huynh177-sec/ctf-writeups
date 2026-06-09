Bước 1: Kiểm tra shell cấu hình của bandit26 trong hệ thống.

cat /etc/passwd | grep bandit26

    *Kết quả:* Phát hiện shell mặc định không phải `/bin/bash` mà là một kịch bản tùy chỉnh `/usr/bin/showtext`.

*   **Bước 2:** Đọc mã nguồn của kịch bản vỏ bọc.
    ```bash
cat /usr/bin/showtext
*Kết quả:* Kịch bản gọi lệnh `more ~/text.txt` để in văn bản, sau đó dùng `exit 0` để ngay lập tức ngắt kết nối người dùng.
Bước 3: Lợi dụng cơ chế chờ tương tác của lệnh more. Thiết lập lại kích thước Terminal xuống mức cực nhỏ (5 dòng) để ép more không thể in hết văn bản và phải tạm dừng.

stty rows 5


*   **Bước 4:** Tiến hành đăng nhập bằng SSH key.
    ```bash
    ssh -i bandit26.sshkey bandit26@localhost -p 2220
Bước 5: Do Terminal bị ép nhỏ, lệnh more nghẹn lại và hiện dòng trạng thái chờ --More--. Bấm phím v để kích hoạt trình soạn thảo Vim (tính năng ẩn của lệnh more).

Bước 6: Từ bên trong Vim, thay đổi biến môi trường Shell và bung cửa sổ lệnh mới để thoát khỏi vỏ bọc.

Plaintext
:set shell=/bin/bash
:sh
    *Kết quả:* Vượt ngục thành công, thu được dấu nhắc lệnh `bandit26@bandit:~$`. Lấy được mật khẩu cho Level 26.

### 3. Bài học cốt lõi
*   **Restricted Shell Escape:** Quản trị viên thường giới hạn quyền bằng cách ép người dùng chạy một script cố định thông qua cấu hình `/etc/passwd`.
*   **Vulnerability by Design (GTFOBins):** Các phần mềm có tính tương tác như `more`, `less`, hoặc `vim` đều chứa các lệnh nội bộ cho phép gọi một shell con (spawn shell). Nếu được cấp quyền chạy trong môi trường hạn chế, Hacker có thể dùng chính các công cụ này để lật lọng và leo thang đặc quyền.
*   **Thao tác vật lý (Terminal Resizing):** Việc can thiệp vào không gian hiển thị của Terminal (`stty`) có thể thay đổi luồng thực thi của các chương trình đọc văn bản.

### 4. Công cụ sử dụng
*   `cat`, `grep`: Để trinh sát file cấu hình và đọc mã nguồn.
*   `stty`: Để thao túng số dòng hiển thị của Terminal.
*   `ssh`: Để đăng nhập bằng Private Key (`-i`).
*   `more` & `vim`: Công cụ khai thác (Exploit tools) dùng để bung Shell mới.
