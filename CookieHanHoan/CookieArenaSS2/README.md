# CookieArenaSeason2 
`capy3ra`

## Magic login
1. Trong source code có đoạn xử lý đăng nhặp như sau:
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a7d96d5d-cd05-42e5-b9eb-949879ebc162)
2. Tức là nó sẽ so sánh giá trị password đầu vào sau khi escape và hash sha256 với "0" nếu bằng thì login thành công.
3. Ở đây chúng ta có thể khai thác PHP hash "collisions" cụ thể là floating-point numbers [Ref](https://github.com/spaze/hashes/blob/master/README.md)
4. Loose comparison với "==" làm ta có thể sử dụng '0e1' == '00e2' == '0' để bypass (e ở đây kiểu như lũy thừa, 0e bao nhiều thì kqua vẫn sẽ là 0)
5. Lấy 1 giá trị để khi hash sha256 trả về giá trị có dạng 0e...
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/ec7ff1ac-b6d2-4751-920d-c187e05eaac6)
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/762b690e-16f8-4946-8fd1-478d85747383)
6. Đăng nhập thành công.
7. Redirect tới trang upload.php rồi up file php để lấy flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/eafc7729-05a9-4722-bd71-452a5d1e7e1b)

## Be Positive
1. Bài này cho phép gửi số tiền âm đi
2. Khi gửi đi số tiền âm thì balance của người đấy tăng lên trị tuyệt đối số tiền gửi đi. Đủ để mua flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/c0c48e58-9cbd-4902-a9ce-808c1936dacf)

## 
