## 🟢 Level 0 -> 1
* **Mục tiêu:** Đọc file `readme` nằm trong thư mục hiện tại (home directory).
* **Cách giải:**
  ```bash
  cat readme
Bài học: Cách sử dụng lệnh cat cơ bản để đọc nội dung tệp tin.

🟢 Level 1 -> 2
Mục tiêu: Đọc một file có tên kỳ lạ là -

Cách giải:

Bash
cat ./-
Tại sao lệnh cat - lại bị lỗi? Vì dấu - trong Linux mặc định được hiểu là đầu vào/đầu ra chuẩn (stdin/stdout) chứ không phải tên file. Phải dùng ./- để ép hệ thống hiểu đây là một đường dẫn file trong thư mục hiện tại.

Cách giải khác: Có thể dùng cat -- - hoặc cat < -

🟢 Level 2 -> 3
Mục tiêu: Đọc file có tên chứa khoảng trắng (VD: spaces in this filename).

Cách giải:

Bash
cat "./--spaces in this filename--"
Bài học: * Cần dùng dấu ngoặc kép "" (hoặc nháy đơn '') bao quanh tên file nếu tên đó có chứa dấu cách (khoảng trắng).

Mẹo : Chỉ cần gõ vài chữ cái đầu rồi ấn phím Tab, Linux sẽ tự động điền nốt tên file và tự động xử lý luôn mấy khoảng trắng này!


🟢 Level 3 -> 4
Mật khẩu được giấu trong thư mục inhere và ta phải tìm nó.
Bash
cd inhere
ls -la
cat ./...Hiding-From-You
File có dấu "." được giấu ẩn nên ngay khi dùng lệnh ls -la( hiển thị tất cả các file) ta liền thấy file đó.

🟢 Level 4 -> 5
Vẫn là thư mục inhere, nhưng lần này khi ta tìm file (ls) thì thấy 10 file giống nhau. Mục tiêu là phải đi tìm một file khác biệt với các file còn lại
Lệnh mới: file
Lệnh file  có chức năng kiểm tra và báo cho bạn biết định dạng thực sự của một tập tin là gì (là văn bản, hình ảnh, file nén, hay dữ liệu thô,...).
Ta tiến hành đọc file, để cho nhanh thì ta kết hợp thêm lệnh "*" tức là đại diện cho tất cả các file
Ta sẽ thấy một file ASCII text khác biệt với các file còn lại (file mà chúng ta có thể đọc được)
Bash
cd inhere
ls
ls -la
file ./*
cat ./-file07


