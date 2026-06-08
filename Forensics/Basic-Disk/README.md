writeup: basic disk 2

công cụ dùng: ftk imager, registry explorer, db browser for sqlite

câu 1: tìm build number

em biết build number nằm trong registry hive `SOFTWARE` vì windows lưu toàn bộ thông tin phần mềm và hệ điều hành vào đó, cụ thể là key `Microsoft\Windows NT\CurrentVersion`, 

b1: mở file ảnh đĩa trong ftk imager, vào:

```
[root]\Windows\System32\config\
```

b2: chuột phải vào file SOFTWARE -> export file -> lưu ra desktop

<img width="1410" height="701" alt="image" src="https://github.com/user-attachments/assets/4d2690aa-fc00-4e83-9127-08de32141348" />


b3: mở registry explorer, load file `SOFTWARE` vừa export vào


b4: tìm tới CurrentVersion trong:

```
ROOT\Microsoft\Windows NT\CurrentVersion
```

<img width="1547" height="875" alt="image" src="https://github.com/user-attachments/assets/2bc826d5-9b9d-40b2-b8bd-f01c6d94e1e0" />

b5: nhìn khung bên phải, tìm 2 dòng:

<img width="695" height="370" alt="image" src="https://github.com/user-attachments/assets/33bf5e22-c36f-43cf-804c-4f2a72ab0fd2" />


kết quả câu 1: `Win10-19043`

câu 2: tìm tài liệu bí mật và mật khẩu

 tìm file tài liệu

quay lại ftk imager, bới các thư mục của user k137:


 em chỉ thấy Appdata với Downloads


 <img width="627" height="105" alt="image" src="https://github.com/user-attachments/assets/b15b8eba-ff5d-422f-a412-d0dc469c8cd1" />

 em lục vào appdata và tìm tới history của trình duyệt:

 <img width="810" height="583" alt="image" src="https://github.com/user-attachments/assets/756d82c8-e3d0-4710-b652-568e3627ae38" />

 sau đó export -> Desktop

 và dùng DB Browser đê tìm thêm thông tin 

 <img width="1370" height="322" alt="image" src="https://github.com/user-attachments/assets/6fc85b68-d2b9-4929-b8ad-8ba52f73e591" />

 em thấy user có tải winrar và 1 file exe lạ tên uwu, mà em lục không thấy

 em nghi ngờ đã được xóa nên em vào thùng rác

 <img width="662" height="305" alt="image" src="https://github.com/user-attachments/assets/a772b528-c1e6-4645-a4a7-226a28c5f312" />

 phát hiện các tệp lạ, và em tìm hiểu là 1 tệp khi bị xóa sẽ chia thành 2 phần, phần I là phần info và phần R là phần nội dung  

 em tìm dc 3 file là pass.txt chứ pass: p@$$w0rd3458


và Kaxeetxe.txt chứa Q2F1c2VfSSdkX2RpZV9mb3JfeW91X1lvdV9rbm93X0knZF9zdGlsbF9kaWVfZm9yX3lvdQ==

và Kaceetce.txt chứa aHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g/dj1kUXc0dzlXZ1hjUQ==

khi decode 2 cái ra em được Cause_I'd_die_for_you_You_know_I'd_still_die_for_you và https://www.youtube.com/watch?v=dQw4w9WgXcQ, truy cập vào link thì em bị 

<img width="1907" height="1007" alt="image" src="https://github.com/user-attachments/assets/7b519008-f0f5-48be-a9a3-aebc0f3297d2" />


em thấy Cause_I'd_die_for_you_You_know_I'd_still_die_for_you giống với format đề đưa ra, em nghĩ nó là content, ghép cái flag lại được

KCSC{win10-19043_cause_i'd_die_for_you_you_know_i'd_still_die_for_you}

em không có submit được nên không chắc đúng, tại em tháy còn nhiều cái em chưa khai thác được trong history của trình duyệt



