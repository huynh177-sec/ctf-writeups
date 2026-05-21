## 🟢 Level 10 -> 11
* **Mục tiêu:** Giải mã (decode) mật khẩu được cất trong file `data.txt`. File này đang bị bọc bởi chuẩn mã hóa Base64.
* **Cách giải:**
  Sử dụng công cụ `base64` với cờ `-d` để giải mã:
  ```bash
  cat data.txt | base 64 -d
