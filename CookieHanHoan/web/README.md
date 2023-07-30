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
- [Baby Strcmp](#baby-strcmp)
- [PHP Inclusion to RCE](#php-inclusion-to-rce)
- [Time](#time)
- [Password Disclosure](#password-disclosure)
- [File upload via PUT method](#file-upload-via-put-method)
- [Baby OS Path](#baby-os-path)
- [Simple Blind SQL Injection](#simple-blind-sql-injection)
- [Logger Middleware](#logger-middleware)
- [Blind Logger Middleware](#blind-logger-middleware)
- [HTML to PDF](#html-to-pdf)
- [Command Limit Length](#command-limit-length)
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
7. Tuy nhiên khi nhập thông tin đăng nhập đúng nhưng vẫn không thấy endpoint GET /check.php trả lại bất cứ thứ gì.
8. Nhận ra rằng khi nhập trên trình duyệt các ký tự như `@` sẽ bị encode. Do đó ta sẽ gửi request bằng burp
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/bfd0b011-5f6a-401c-aeac-407d8fe2fa9f)
9. Lấy được flag

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

## Baby Strcmp

1. Ở bài này web so sánh giá trị param flag truyền vào với secret key trên server bằng hàm strcmp
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/0b71e88a-9a0d-479b-93d0-776b1bacd30d)
2. Research ra được cách bypass hàm strcmp bằng cách truyền một mảng rỗng vào param flag. Để rồi trong php strcmp nó coi nó là giá trị NULL và == 0 trả về true
3. Sửa request
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/e758a349-8ab9-40d6-89ec-aaedb6cb54a7)

## PHP Inclusion to RCE

1. Bài này bị dính LFI.
2. Có thể bypass traversal sequences stripped non-recursively bằng cách dùng payload
`?file=...//....//....//etc/passwd`
3. Đọc được file log của nginx.
4. Tiến hành LFI to RCE via log poison.
5. Gửi request với user-agent với mã php malicious
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/7a37e143-251a-4489-a915-68b44fd878fe)
6. Đọc thành công các file ở thư mục root
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/da43aabf-0698-45d5-a118-12e7f8f0ad89)
7. Đọc file flagOqfu8.txt để lấy flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/31c8d137-8d6f-4682-86d3-c165afca3897)

## Time

1. Dễ thấy bài này bị dính command injection.
2. Command được sử dụng trong bài có vẻ là `date +'format'` với param format được lấy trên url
3. Sau khi thử test một vài payload thì nhận thấy có vẻ như sau khi tham số format được truyền vào thì sẽ thêm `';` vào cuối câu lệnh trước khi truyền vào hàm thực thi.
4. Đây là đoạn xử lý trong model.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/1ef329d7-7229-4019-bb24-879cbd3b9e80)
5. Tuy nhiên khi dùng dấu `#` để comment thì vẫn không nhận dược kết quả như mong muốn.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/9a114b97-d1ab-4b7c-bf53-b0aff1cbd41e)
6. Encode `#` thành `%23` thì bypass thành công. Có lẽ là do trình duyệt cho rằng `#` khai báo fragment

## Password Disclosure

1. Ở bài này chúng ta có source code.
2. Fuzz 1 vòng trang web nhận thấy lỗi disclosure ở trang reset password khi mà nó đắt giá trị password hiện tại vào ô input. Như vậy chỉ cần gửi được request reset-password cho admin thì ta sẽ có được mk của admin.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/5d06d98f-6fb2-4b5a-a9d1-32fcd5a78d47)
3. Nhận thấy trong source code đoạn xử lý GET request reset-password thì nó cho phép lấy 2 phương thức. 1 là như mặc định là lấy session của user. 2 là thông qua param username -> Từ đó nhận ra ta có thể bypass nó.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/926ff9f8-1bde-4c87-96a6-7bfbaa2269d0)
4. Có được password -> login vào lấy flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/d9083982-e26f-4d9d-b9c3-afc268ca931f)

## File upload via PUT method

1. Theo đề bài thì ta có thể upload bằng PUT method.
2. Thử upload file PUT `test2.php` để xem thông tin php
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/f8bcda98-31ed-4f66-941f-15e6d67ec359)
3. Thành công
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a28b12f5-50bc-4c18-b147-239694fa9a79)
4. Thực thi câu lệnh ls / qua php
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/839dba85-c4d9-4a2a-a6e1-4143756a8870)
5. Lấy flag qua flag.txt
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/1ae3be8b-cb55-4625-b02c-e5404f1953ae)

## Baby OS Path

1. Bài này có cho ta source code. Nhận thấy rằng flag.txt nằm ở root directory.
2. Trong đoạn code xử lý GET path dùng hàm `os.path.join` với tham số thứ nhất được set là thư mục `statics`.
3. Tuy nhiên ta có thể bypass hàm này bằng cách truyền thẳng vào đường dẫn tuyệt đối như sau:
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/2f194204-70fe-4f2b-b76a-9a58254a15e1)
4. Từ đó khai thác bằng cách truy cập path `http://103.97.125.53:31565//flag.txt`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/377c4cfb-bcd0-4708-9692-b6d64d41ddb1)

## Simple Blind SQL Injection

1. Ở bài này chúng ta có lỗi Blind SQLI, kết quả trả về chỉ trả về lỗi (ví dụ như sai syntax hay query một cột không có trong bảng...) hoặc nếu câu query trả về true thì nhận exists còn không thì trả về not found.
2. Sử dụng `substring admin' AND SUBSTRING((SELECT sql FROM sqlite_master WHERE tbl_name = 'users'), 20, 1) = '('--` để lấy câu truy vấn sql tạo bảng users. Như một câu điều kiện để xác định từng ký tự một :((. Bằng cách brute-force các ký tự. Hết cách :((.
3. Sau 1 tỷ lần thử thì ta tìm được tên cột chứa password là `upw` từ câu query không đầy đủ.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/bf45ed8e-6c82-45a3-b746-3886a7a7559c)
4. Tiếp tục brute force password bằng `admin' AND SUBSTRING((SELECT upw FROM users WHERE uid='admin'), 2, 1) = '0'--` có được password là `y0u_4r3_4dm1n`.
5. Đăng nhập lấy flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/737da063-7ff1-4632-8b7e-9598dad3c61b)

## Logger Middleware

1. Trang web được cho sẽ lưu lại access log vào db rồi hiển thị lại cho client.
2. Ở đây do không thể nhập đầu vào, nên ta sẽ sử dụng header attack vào Header `User-Agent` để có thể truyền input vào log.
3. Sau khi gửi request tới header `User-Agent: cuong',)` ta nhận được một lỗi sql và cho biết câu lệnh insert log.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/cadae0a5-da1a-47fa-9b97-00bc3d8fd1a4)
4. Đặt các giá trị cho các trường con lại và để dấu comment ở cuối. Nhận thấy rằng ta đã có thể insert log thành công.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/e8b9b2e3-8553-4a21-a51a-3ba58f77a8bc)
5. Sử dụng payload `User-Agent: cuong','None','None','None','None') UNION SELECT name,1,2,3,4,5 FROM sqlite_master WHERE type = 'table';--` để lấy tên các bảng trong database thông qua UNION query (Bởi trang không cho phép chạy 2 câu lệnh một lúc)
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/1fc84abc-1e2b-4054-b0e1-94f4f18bcd1c)
6. Biết được flag nằm trong bảng flag. Dùng payload `User-Agent: cuong','None','None','None','None') UNION SELECT  sql,1,2,3,4,5 FROM sqlite_master WHERE tbl_name = 'flag';--` để lấy câu truy vấn tạo bảng flag -> Có được cột chứa flag `secr3t_flag`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/d18acbf2-1974-483d-9cfb-3a1a6c077b9f)
7. Query lấy flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/f4512f15-56a0-4b32-9941-818a19c16394)

## Blind Logger Middleware

1. Bài này có chắc năng giống như bài [Logger Middleware](#logger-middleware) nhưng chỉ có chức năng hiển thị xem truy cập đã được log lại chưa và không hiện thị access log.
2. Khi thử payload tạo lỗi syntax trong sql thì nhận được thông báo `Error`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/03aece90-7c68-48b2-8a44-d70dd5bd12cd)
3. Còn khi truyền đủ các biến giá trị và comment phần thêm sau của câu truy vấn thì nó đã được log thành công. Vậy có thể thấy rằng đây là blind sql nên ta chỉ có thể tìm flag bằng các condition query.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/8a1e21f5-1cc1-4faa-9444-525526e5ba72)
4. Ta dùng func CASE để tạo câu điều kiện.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/ba3b137d-0f77-4ba2-91b6-6a9f3a86f2a7)
5. Để có thể nhận biết biểu thức truyền vào là đúng hay sai ta sẽ dùng function load_extension cụ thể payload dạng như sau `cuong','None','None','None',CASE WHEN  1=1 THEN 2 ELSE load_extension(1) END);--` ở đây khi truyền vào biểu thức đúng thì response trả về Logged còn khi biểu thức sai thì gọi đến hàm load_extension(1) mà giá trị 1 không xác định nên response trả về Error.
6. Xác định các bảng và cột chứa flag. Xác định bảng chứa flag là `flag`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/547a9051-dbbe-4c54-be55-879b92d276d3)
7. Để lấy được tên cột của bảng flag ta sẽ vừa brute-force vừa đoán các ký tự trong câu lệnh sql tạo ra bảng flag với payload `cuong','None','None','None',CASE WHEN  ((substr((SELECT sql from sqlite_master WHERE tbl_name="flag"),1,1))='C') THEN 1 ELSE load_extension(1) END);--` (Dùng intruder)
8. Sau một thời gian mò từng ký tự, ta đọc được sương sương câu truy vấn có dạng. Cột chứa flag có lẽ là cột secret.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/5fdb2a33-4d90-4330-8250-7e168f1b1187)
9. Tiếp theo là brute-force flag có dạng CHH{} với 30 ký tự. Payload `cuong','None','None','None',CASE WHEN  ((substr((SELECT secret from flag),2,1))='H') THEN 1 ELSE load_extension(1) END);--`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/ab92f7e7-ac5b-47c4-b3ac-912ca9306550)

## HTML to PDF
### Ref

1. Bài này web có tính năng convert từ html sang pdf. Sau khi fuzz thì detect được rằng web dùng api của trang [weasyprint.org](weasyprint.org). Và được encode FlateDecode.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/cf640b6f-8a5f-4a9b-aaff-973940b5d7d2)
2. Ta sẽ tạo một host với payload `<link rel=attachment href="file:///etc/passwd">`.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a3c4b793-52b9-4a37-bea6-0f79776c71c5)
3. Sau đó sử dụng script [này](gist.githubusercontent.com/averagesecurityguy/ba8d9ed3c59c1deffbd1390dafa5a3c2/raw/cf89a69253f3618605025f9918f3b08677b879c3/pdf_flatedecode.py) để đọc FlateDecode stream.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/c8b7b990-3b18-4ece-a511-0d73583ccb7c)
4. Đổi payload thành `<link rel=attachment href="file:///flag.txt">`.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/56b96ff2-ae61-4b1a-9376-a0e05edfa7cf)

## Command Limit Length

1. Bài này cho phép rce nhưng lại giới hạn input < 5 ký tự.
2. Thử một vài câu lệnh ngắn như `dir` thì được.
3.  Dựa vào một [bài](https://github.com/bennofs/docs/blob/master/hitcon-2017/baby-first-revenge2.md) tương tự để khai thác
4.  
