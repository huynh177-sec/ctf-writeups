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
