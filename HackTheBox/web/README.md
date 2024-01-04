# Web challenger writeup
`capy3ra`

- [LoveTok](#lovetok)

## LoveTok

1. Đây là một bài whitebox nên ta được cung cấp source code.
2. Sau khi đọc source, ta có thể biết được luồng xử lý của app. Ở đây hệ thống sẽ xử lý theo mô hình MVC. Với model TimeModel thực hiện khảo tạo một đối tượng ước tính thời gian với
3. Đối tượng TimeModel được khởi tạo với param `format` được truyền vào
```
public function index($router)
    {
        $format = isset($_GET['format']) ? $_GET['format'] : 'r';
        $time = new TimeModel($format);
        return $router->view('index', ['time' => $time->getTime()]);
    }
```
4. Với hàm khởi tạo model được thực hiện sẽ tạo ra một thuộc tính `prediction` được random một khoảng thời gian
5. Sau đó ở view index biến time sẽ được gán cho kết quả của phương thức getTime() với đoạn code như sau:
```
public function getTime()
    {
        eval('$time = date("' . $this->format . '", strtotime("' . $this->prediction . '"));');
        return isset($time) ? $time : 'Something went terribly wrong';
    }
```
6. Hàm eval sẽ thực hiện một câu lệnh php động với 2 tham số `format` được nhập vào và `prediction` được random ra từ phương thức khởi tạo ở trên để gán giá trị cho biến time.
7. Với tham số format mà ta kiếm soát ta sẽ cố gắng inject vào nó. Hàm `addslashes` được sử dụng để escapse các dấu `'`,`"` nhưng thế vào chưa đủ.
8. Ý tưởng ở đây là ta sẽ chèn đoạn sau `${system($_GET[1])}` vào hàm eval rồi thêm tham số `1` để exec shell.

![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/72279455-41e0-4030-92c0-5933e4098502)

9. RCE thành công. Đọc flag.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/55d05f7e-1bd9-4f7f-a2fa-580158e1e3d7)

## Toxic

1. Whitebox. Qua phân tích có thể thấy app sẽ tạo ra một object `PageModel` với thuộc tính `file`, rồi nội dung trong sẽ là file được include
2. Có thể đọc file bằng cách thay đổi giá trị thuộc tính rồi serialize lại.
3. Ở bài này, ta có thể đọc được file access.log

![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/87798192-18b6-4525-a44b-61d31ab42a5f)

4. Chèn mã php thông qua user-agent thì nhận thấy rce thành công.

![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/911ef94f-3b4a-49a0-9088-771f825e4044)

## 
