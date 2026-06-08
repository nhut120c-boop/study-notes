# Challenge CTF Forensics
Name challenge: Bitlocker-1
Link: https://play.picoctf.org/practice/challenge/453?category=4&difficulty=2&page=1.

Name user: Lê Minh Nhựt (AT22N0219).

Đầu tiên mình tải file về máy ảo kali để tiện cho việc phân tích và giải mã.

Sau khi tải mình được 1 file tên bitlocker-1.dd, xác định được đây là 1 file img, dựa vào tên bài thì mình đoán được 90% file đang bị khóa dưới dạng bitlocker.
Để chắc ăn mình xài lệnh
```bash
file  bitlocker-1.dd
```
Ta có kết quả 
<img width="1907" height="95" alt="image" src="https://github.com/user-attachments/assets/e000b7a7-c3dd-4c5a-9beb-2bfa141ce2dc" />
Mình nhận ra dạng bài yêu cầu giải mã phân vùng bitlocker. 

Mình bắt đầu dùng công cụ bitlocker2john để trích xuất hash của file 
```bash
bitlocker2john -i bitlocker-1.dd > ma.txt
```
Khi trích xuất xong mình được 1 file ma.txt chứa toàn bộ mã băm của file gốc.
<img width="1679" height="355" alt="image" src="https://github.com/user-attachments/assets/a524b586-452c-4a91-a7ef-f50417f88829" />
Sau đó mình dùng tiếp công cụ thứ 2 là john để crack file đó bằng wordlist từ file rockyou.txt mình có sẵn.
```bash
john ma.txt --wordlist=rockyou.txt
```
Sau 1 lúc thì máy ảo bị đơ, mình tắt và nâng từ 2 luồng lên 6 luồng.
Kết quả được:
<img width="877" height="294" alt="image" src="https://github.com/user-attachments/assets/176aa7b3-52b2-4a52-9578-72c8e4ca2566" />

Ta dễ dàng thấy pass là jacqueline

Tiếp theo mình tạo 1 thư mục để chứa dữ liệu chuẩn bị trích ra
```bash
mkdir kq 
```
Sau đó dùng lệnh 
```bash
sudo dislocker -V bitlocker-1.dd -ujacqueline -- kq
```
Sau đó ta chuyển sang quyền root để truy cập vào folder kq
```bash
sudo su
```
Xong mình dùng cd kq để vào kq, mình thấy 1 file dislocker-file, mình tiếp tục tạo mount để xem tiếp file đó có gì.
<img width="367" height="161" alt="image" src="https://github.com/user-attachments/assets/782742eb-b51f-4e6e-8d62-e33ca3db21ae" />

<img width="612" height="157" alt="image" src="https://github.com/user-attachments/assets/73dc6fa3-5225-4b28-9b62-2189f6ff3aa1" />

Sau khi mount xong thì mình cd vào thư mục xuat để check file.
<img width="517" height="183" alt="image" src="https://github.com/user-attachments/assets/19ee4444-8e55-429a-8f56-99209e253a8d" />
Mình thấy 1 file flag.txt, cat file đó bằng
```bash
cat flag.txt
```
Ta có flag là picoCTF{us3_b3tt3r_p4ssw0rd5_pl5!_3242adb1} 
<img width="376" height="115" alt="image" src="https://github.com/user-attachments/assets/adbc966c-288b-4d5d-89b1-2cc9b49419f3" />









