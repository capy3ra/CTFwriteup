# Cookie Hân hoan (Web hacking)
`capy3ra`

- [Baby Address Note](#baby-address-note)
- [Baby ping](#baby-ping)
- [Simple SQLi](#simple-sqli)
- [Hello Session](#hello-session)
- [Duplicate Content](#duplicate-content)
- [Baby Logger Middleware](#baby-logger-middleware)
- [Ethical Ping Pong Club](#ethical-ping-pong-club)
- [Baby JS Fck](#baby-js-fck)
- [Cookie and Milk](#cookie-and-milk)
- [File Download](#file-download)
- [Password Reset](#password-reset)
## Baby Address Note

1. Dựa vào source code biết được bài này là sql injection. Với câu truy vấn `f"SELECT * FROM users WHERE uid='{uid}';"` ta có thể bypass bằng `' OR '1'='1' --`
2. Khi thử 2 câu truy vấn bằng dấu ; thì nhận được message lỗi là chỉ cho phép 1 câu truy vấn.
3. Được biết db dùng sqlite3 nên ta sẽ dùng union select tới bảng sqlite_master để xem danh sách các bảng trong sql
```
' UNION SELECT sql,name,tbl_name from sqlite_master --
```
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/d695da1d-c61d-4f32-a065-856eccfe8c13)
4. Nhận thấy bảng chứa flag. Lấy flag bằng ``' UNION SELECT 1,2,flag from flag_9AbM4 --``

## Baby Ping

1. Ở bài này, trang web không lọc input mà đã chuyển xuống hẹ thoogsn-> Command Injection 

2. Payload: ``127.0.0.1";cat /flag.txt;echo "`` thì câu lệnh hoàn chỉnh sẽ là ``ping -c 3 "127.0.0.1";cat /flag.txt;echo""``

![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/fba14ada-fb88-4dc5-b625-5b3ea412a5d9)

## Simple SQLi

1. Trong source code cho biết là table user được thêm vào các user guest và admin có userid = guest và userlevel = 0
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/74da77aa-b784-4ad2-b893-46b11280bd4d)
2. Tuy nhiên khi enter userlevel = 0 thì nó chỉ hiển thị record đầu tiên là guest (Nếu có 2 record có cùng giá trị userlevel thì code chỉ xử lý reocrd đầu tiên)
3. Payload: ``0' AND userid = 'admin' --`` để câu truy vấn trở thành ``select * from users where userlevel='0' AND userid = 'admin' --'``
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/f2d2bd35-793b-4f40-9223-4a261141df7e)

## Insecure Download File

1. Khi lần đầuu down transcript ta thấy file down về là 2.txt.
2. Thử đọc file 1.txt thì có được flag

## Hello Session

1. Trong source code có thấy một endpoint khả nghi ``/manager``
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/94241d61-58ec-491b-a45e-bab97b7719de)

2. Truy cập endpoint đó thì nhận đưuọc session của admin
3. Thay sessionid

## Duplicate Content

1. Ở bài này người dụng có chắc năng clone lại bài viết của mình. Tuy nhiên ở đây do lỗi Unauthorized nên từ tk guest có thể clone lại bài viết của người dùng khác.
2. Brute force GET /clone?id=$1$ để clone lại tất cả các bài viết trên trang blog về tài khoản guest rồi tìm flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/429fdb86-a166-4d6c-a2e6-d540868d06df)
3. Trở lại trang chủ thì thấy flag ở blog_id = 22

## Baby Logger Middleware

1. Mục tiêu của bài là cố inject vào cột ip_addr trong access log lưu trên database
2. Khi thử chèn các header như `X-Forwarded-For` không được
3. Thử sqli thì nhận được lỗi và detect sqli
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/880e9167-9f9b-4e1b-a86b-5feb04638169)
4. Thì error message ta biết được caauu insert là `INSERT INTO logger (ip_address, user_agent, referer, url, cookie, created_at) VALUES ('***', 'manhcuon','none','none','none','none')`
5. Insert thêm record có id_addr bằng `HACKER_WAS_HERE`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/e7ce732d-daf4-4df6-9416-3d6d5e4a0253)

## Baby JS Fck

1. Trong bài ta có một đoạn code xử lý đăng nhập như sau:
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/e3807bc2-60c8-4b74-b4f3-adb05cf9afdd)
2. Bật Inspect vào source nhận thấy có 4 file js bị obfuscation
3. Vào console nhập hàm verify ta có username và password
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/bc322b8a-633e-417e-97b6-54ef0f8e9fcc)
4. Còn hàm verifyRole thì là một hàm xử lý trong các giá trị trong js mà khi giải được ta có input là `@mdiN` thì sẽ trả về giá trị true
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/b7c7d1b8-5c56-418b-beba-706c4cb361a8)
5. Thông tin đăng nhập
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/30dd278a-c573-49ee-b825-2d78f265a7c8)
6. Hoặc có thể decode js fck language ở trang [này](https://enkhee-osiris.github.io/Decoder-JSFuck/)

## Cookie and Milk

1. Bài này được cung cấp source php khi chúng ta truy cập vô challenge
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/ae385a0c-9c29-4e69-bf59-187bba998c42)
2. Function `extract` để lấy tất cả các tham số mà chúng ta đưa vào bằng GET method. (vuln đầu tiên của bài này cũng ở đây). Giải thích về hàm này thì khi chúng ta truyền các tham số thì sẽ ghi đè được các biến internal.
3. Ngoài ra ở dưới bài dùng "==" để so sánh 2 giá trị hash gây ra vuln thứ 2 loose comparison.
4. Tuy nhiên nó không cho phép 2 giá trị này bằng nhau nên ta sẽ lấy payload ở [đây](https://github.com/spaze/hashes/blob/master/sha1.md)
5. Payload: `https://cookie-and-milk-2e70038f.dailycookie.cloud/?_SESSION[magic]=aaroZmOk&_COOKIE[magic]=aaK1STfY`
6. Nhận được flag
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/1c5c6170-0b35-43af-8f65-f2bcf561783e)

## File Download

1. Upload file text nhưng không thể inject html do < > đã bị encode
2. Sau khi upload một file thứ path travelsal thì nhận thấy site bị dính vuln này

## Ethical Ping Pong Club

1. Có thể xác định luon từ đầu bài này chứa vuln os command inject
2. Khi thử các Chaining commands như `; & && | || ` đều không được
3. Thử với xuống dòng thì inject thành công
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/91aab036-8ccb-43d0-9fcf-f870e0fab417)
4. Cat flag.txt nhưng bài filter dấu space.
5. Bypass space bằng dấu `<`.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/c3e6e6a1-a489-49d5-941b-e0399e9d6038)

## Password Reset

1. Bài này có chức năng reset password chứa lỗ hổng.
2. Khi gửi post request reset-password thì server sẽ lấy giá trị host để generate ra link reset password.
3. Lợi dụng điều đó chèn host header tới host ta kiểm soát với email của admin
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/969d0a38-bc63-4480-8597-29c641370340)
4. Sau đó vào burp collaborator nhận được token
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/8e3f627b-f93d-484a-852c-829d3f6c830b)
5. Đổi mk rồi login vào account admin lấy flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/b696cb93-e5b7-4aee-b422-6e7bde73a69c)

## 
