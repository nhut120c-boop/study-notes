Disk Analysis & Autopsy
---
câu 1
câu hỏi là 
```
What is the MD5 hash of the E01 image?
```
để làm câu này em vào Autopsy mở case lên, chuột phải HASAN2.E01 -> View Summary Information

<img width="1522" height="812" alt="image" src="https://github.com/user-attachments/assets/cd7c8de2-db12-43aa-bdc5-df9f4226930f" />

rồi vào container, thấy MD5

<img width="1522" height="770" alt="image" src="https://github.com/user-attachments/assets/8e46d0c3-fefa-41e0-a513-42f2e40df528" />

đáp án là
```
## câu 2

câu hỏi là:

```text
What is the computer account name?
```

với câu này, em hiểu “computer account name” là tên máy tính của Windows trong disk image

```text
ComputerName
```

Autopsy trả về nhiều kết quả có chứa chuỗi này. Sau khi xem phần preview, em thấy có dòng:

```text
ComputerName = DESKTOP-0R59DJ3
```

<img width="1535" height="777" alt="image" src="https://github.com/user-attachments/assets/7704aaf4-9ac0-451b-b08b-6f2cf3317c69" />

đáp án

```text
DESKTOP-0R59DJ3
```
câu 3 


câu hỏi:

```text
List all the user accounts. (alphabetical order)
```

em vào thư mục:

```text
/Users
```

trong Autopsy để xem các user profile. Sau đó loại các thư mục mặc định của Windows như:

```text
All Users
Default
Default User
Public
```

các user account còn lại là:

```text
H4S4N, joshwa, keshav, sandhya, shreya, sivapriya, srini, suba
```
<img width="1530" height="757" alt="image" src="https://github.com/user-attachments/assets/b18a1fc5-623a-4cc1-8a4e-ba14c8bd0ba2" />

đáp án 
```
H4S4N, joshwa, keshav, sandhya, shreya, sivapriya, srini, suba
```
câu 4
## câu 4

câu hỏi:

Who was the last user to log into the computer?

em vào:

Results -> Extracted Content -> Operating System User Account

ở bảng này em kiểm tra danh sách user account và thông tin logon/access liên quan. user đúng với câu hỏi là:

sivapriya

<img width="1518" height="787" alt="image" src="https://github.com/user-attachments/assets/1585ddd0-910a-4555-a9c6-3bb0c8a2d38c" />

đáp án:

sivapriya

