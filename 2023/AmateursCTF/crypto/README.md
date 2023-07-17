`capy3ra`

## Compact XORs

1. Đề bài cho flag đã được encode hex. Dựa vào các dữ kiện từ đề bài ta có:
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/789824de-b1c3-4242-8b18-aa5a53ff5ea0)
2. Nhận thấy rằng ở các ô index lẻ trùng với giá trị index của format flag.
3. Còn các ô index chẵn của chuỗi flag là kết quả của phép xor giá trị index chuỗi trong encoded fleg và 61(hex)
