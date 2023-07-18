`capy3ra`

## Compact XORs

1. Đề bài cho flag đã được encode hex. Dựa vào các dữ kiện từ đề bài ta có:
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/789824de-b1c3-4242-8b18-aa5a53ff5ea0)
2. Nhận thấy rằng ở giá trị hex byte ở đầu và những hex byte cách nhau 1 có giá trị khi chuyển sang ascii giống với format flag.
3. Còn những hex byte còn lại trong flag bằng hex byte trong `fleg` xor với hex byte trước nó.
4. Sử dụng đoạn code py sau để generate ra flag.

```python
for i in range(0, len(arr)):
    if i%2==0:
        flag += chr(int(arr[i], 16))
    else:
        xor_cal = str(int(arr[i], 16) ^ int(arr[i-1], 16))
        flag += chr(int(xor_cal , 10))
```
![image](https://github.com/cuong9cm/CTFwriteup/assets/80744099/3712528d-6f70-464e-a677-884d80d10e9e)

## 
