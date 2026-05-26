## 🟢 Level 10 -> 11
* **Mục tiêu:** Giải mã (decode) mật khẩu được cất trong file `data.txt`. File này đang bị bọc bởi chuẩn mã hóa Base64.
* **Cách giải:**
  Sử dụng công cụ `base64` với cờ `-d` để giải mã:
  ```bash
  cat data.txt | base 64 -d

## 🟢 Level 11 -> 12
* **Mục tiêu:** Giải mã mật khẩu trong file `data.txt`. File này đã bị mã hóa bằng thuật toán xoay 13 vị trí (ROT13 cipher).
* **Cách giải:**
  Sử dụng lệnh `tr` (translate) để ánh xạ và thay thế các ký tự bị dịch chuyển:
  ```bash
  cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'


  ## 🟢 Level 12 -> 13
* **Mục tiêu:** Tìm mật khẩu bị giấu sâu trong một file Hexdump. File này thực chất là một "con búp bê Nga", bị đóng gói và nén liên tục qua nhiều lớp (`gzip`, `bzip2`, `tar`).
* **Cách giải (Tóm tắt quy trình):**
  1. Tạo không gian làm việc an toàn: `mkdir /tmp/workspace` và copy file vào đó.
  2. Dịch ngược Hexdump thành file gốc: `xxd -r data.txt > file_bi_nen`
  3. Lặp lại **Quy trình 3 nhịp** cho đến khi ra lõi (ASCII text):
     * **Nhịp 1:** Dùng `file <tên_file>` để soi xem nó đang bị bọc bởi lớp vỏ gì.
     * **Nhịp 2:** Dùng `mv` đổi đuôi file cho đúng với chuẩn của lớp vỏ đó (chỉ áp dụng với `gzip` và `bzip2`).
     * **Nhịp 3:** Dùng lệnh giải nén tương ứng:
       - Nếu là gzip: `gzip -d <file.gz>`
       - Nếu là bzip2: `bzip2 -d <file.bz2>`
       - Nếu là tar: `tar -xf <file.tar>`
* **Bài học cốt lõi:**
  * File extension (đuôi file) trong Linux không quyết định bản chất của file. Luôn phải dùng lệnh `file` để xác minh (dựa vào Magic Bytes).
  * Hiểu sự khác biệt cơ bản giữa việc đóng gói nguyên vẹn (Archive - `tar`) và nén giảm dung lượng (Compress - `gzip/bzip2`).
  * `xxd -r`: Công cụ kinh điển để phục hồi file từ dạng mã Hex.
  * `bandit12@bandit:~$ mkdir /tmp/viet_huynh
bandit12@bandit:~$ ls
data.txt
bandit12@bandit:~$ cp data.txt /tmp/viet_huynh/
bandit12@bandit:~$ cd /tmp/viet_huynh
bandit12@bandit:/tmp/viet_huynh$ xxd -r data.txt > file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file file_bi_nen
file_bi_nen: gzip compressed data, was "data2.bin", last modified: Fri Apr  3 15:17:20 2026, max compression, from Unix, original size modulo 2^32 566
bandit12@bandit:/tmp/viet_huynh$ mv file_bi_nen file_bi_nen.gz
bandit12@bandit:/tmp/viet_huynh$ gunzip file_bi_nen.gz
bandit12@bandit:/tmp/viet_huynh$ ls
data.txt  file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file file_bi_nen
file_bi_nen: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/viet_huynh$ mv file_bi_nen file_bi_nen.bz2
bandit12@bandit:/tmp/viet_huynh$ bunzip2 file_bi_nen.bz2
bandit12@bandit:/tmp/viet_huynh$ ls
data.txt  file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file file_bi_nen
file_bi_nen: gzip compressed data, was "data4.bin", last modified: Fri Apr  3 15:17:20 2026, max compression, from Unix, original size modulo 2^32 20480
bandit12@bandit:/tmp/viet_huynh$ mv file_bi_nen file_bi_nen.gz
bandit12@bandit:/tmp/viet_huynh$ gunzip file_bi_nen.gz
bandit12@bandit:/tmp/viet_huynh$ ls
data.txt  file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file file_bi_nen
file_bi_nen: POSIX tar archive (GNU)
bandit12@bandit:/tmp/viet_huynh$ tar xf file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ ls
data5.bin  data.txt  file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/viet_huynh$ tar xf data5.bin
bandit12@bandit:/tmp/viet_huynh$ ls
data5.bin  data6.bin  data.txt  file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/viet_huynh$ mv data6.bin data6.bin.bz2
bandit12@bandit:/tmp/viet_huynh$ bunzip2 data6.bin.bz2
bandit12@bandit:/tmp/viet_huynh$ ls
data5.bin  data6.bin  data.txt  file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file data6.bin
data6.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/viet_huynh$ tar xf data6.bin
bandit12@bandit:/tmp/viet_huynh$ ls
data5.bin  data6.bin  data8.bin  data.txt  file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Fri Apr  3 15:17:20 2026, max compression, from Unix, original size modulo 2^32 49
bandit12@bandit:/tmp/viet_huynh$ mv data8.bin data8.bin.gz
bandit12@bandit:/tmp/viet_huynh$ gunzip data8.bin.gz
bandit12@bandit:/tmp/viet_huynh$ ls
data5.bin  data6.bin  data8.bin  data.txt  file_bi_nen
bandit12@bandit:/tmp/viet_huynh$ file data8.bin
data8.bin: ASCII text
bandit12@bandit:/tmp/viet_huynh$ cat data8.bin `


## 🟢 Level 13 -> 14
* **Mục tiêu:** Đăng nhập vào tài khoản `bandit14` bằng file SSH Private Key thay vì dùng mật khẩu truyền thống.
* **Cách giải:**
  1. Mở một Terminal mới tại máy tính cá nhân (Local machine), dùng lệnh `scp` để tải file chìa khóa từ máy chủ Bandit về máy:
     ```bash
     scp -P 2220 bandit13@bandit.labs.overthewire.org:~/sshkey.private .
     ```
  2. Thiết lập quyền bảo mật khắt khe cho file chìa khóa (bắt buộc, nếu không SSH sẽ từ chối kết nối):
     ```bash
     chmod 600 sshkey.private
     ```
  3. Dùng chiếc chìa khóa vừa tải về để "mở cửa" bước thẳng vào tài khoản `bandit14`:
     ```bash
     ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
     ```
  4. Đọc mật khẩu tại hệ thống đích:
     ```bash
     cat /etc/bandit_pass/bandit14
     ```
* **Bài học cốt lõi:**
  * **Lệnh `scp` (Secure Copy):** Công cụ đắc lực của Sysadmin để truyền tải tệp tin xuyên Internet thông qua giao thức SSH an toàn. Cần đặc biệt chú ý đến cú pháp: cờ `-P` in hoa cho cổng, và tuyệt đối không để thừa dấu cách (space) trong đường dẫn nguồn.
  * **Đặc tính của Private Key:** Trong Linux, file Private Key mang quyền lực tối cao nên bắt buộc phải được bảo vệ bằng quyền `600` (chỉ chủ sở hữu mới có quyền Read/Write, tước toàn bộ quyền của người ngoài).
