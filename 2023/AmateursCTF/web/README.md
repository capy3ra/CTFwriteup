`capy3ra`

## Waiting-an-eternity 

1. Khi truy cập site có thể bắt được path tới trang secret-site + secret code.
2. Truy cập vào path đó thì nhận thấy có một giá trị cookie `time` mà khi thay đổi nó bộ đếm thời gian ta đã chờ đợi cũng thay đổi.
3. Khi mà ta thay đổi giá trị cookie này thành một giá trị rất lớn thì nhận được flag

## Funny Factorials

1. Bài này cho chúng ta source code và từ đó ta có thể biết được tham số themes đã được filter path travelsal bằng hàm filter_path()

```python
def filter_path(path):
    # print(path)
    path = path.replace("../", "")
    try:
        return filter_path(path)
    except RecursionError:
        # remove root / from path if it exists
        if path[0] == "/":
            path = path[1:]
        print(path)
        return path
```

2. Ở đây hàm filter_path() sẽ thay các chuỗi `../` thành NULL theo kiểu đệ quy và cắt chuỗi nếu giá trị ở index = 0 là `/`
3. Dùng payload `..///////..////..//////etc/passwd` để bypass hàm filter trên.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/e7939870-5149-4348-a9ca-2d3e55a8948f)
4. Từ đó lấy được flag.
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/0224d7a4-5c4f-4b63-b544-097a50ae4d0c)

## 
