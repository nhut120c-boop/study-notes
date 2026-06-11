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
```
sivapriya
```
câu 5

câu hỏi:

```text
What was the IP address of the computer?
```

ban đầu em kiểm tra trong registry `SYSTEM` vì IP của Windows thường nằm ở phần network interface:

```text
Windows\System32\config\SYSTEM
```

nhưng giá trị tìm được là:

```text
DhcpIPAddress = 0.0.0.0
```
<img width="1536" height="697" alt="image" src="https://github.com/user-attachments/assets/a954143f-a134-4c55-88aa-c548f87317bc" />

nên không lấy được IP thật từ chỗ này

sau đó em kiểm tra các chương trình liên quan tới mạng trong `Program Files (x86)` và thấy phần mềm `Look@LAN`. Đây là tool liên quan tới mạng LAN nên có thể lưu IP local của máy

em mở file cấu hình:

```text
Program Files (x86)\Look@LAN\irunin.ini
```

trong file này có dòng:

```text
LANIP=192.168.130.216
```

<img width="1532" height="762" alt="image" src="https://github.com/user-attachments/assets/7d606ace-1aa8-4a38-92fc-1cc3b82ec3de" />


`LANIP` là IP nội bộ của máy trong mạng LAN, nên đáp án là:

```text
192.168.130.216
```


câu 6

câu hỏi:

```text
What was the MAC address of the computer? (XX-XX-XX-XX-XX-XX)
````

em kiểm tra tiếp file cấu hình của Look@LAN:

```text
Program Files (x86)\Look@LAN\irunin.ini
```

trong file có dòng:

```text
LANNIC=0800272cc4b9
```

<img width="1528" height="717" alt="image" src="https://github.com/user-attachments/assets/12d6f7d5-6bbf-4767-b6df-09687660e818" />


`LANNIC` là thông tin card mạng LAN. Đề yêu cầu format `XX-XX-XX-XX-XX-XX`, nên em tách giá trị này thành:

```text
08-00-27-2C-C4-B9
```

đáp án:

```text
08-00-27-2C-C4-B9
```
câu 7

câu hỏi:

```text
What is the name of the network card on this computer?
```

ban đầu em nghĩ tên card mạng sẽ nằm trong registry `SYSTEM`, nên thử kiểm tra:

```text
Windows\System32\config\SYSTEM
```

và nhánh network adapter class:

```text
ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
```

nhưng lúc thử load hive bằng `regedit` thì bị rỗng / không đọc được đúng hive của image. Có lúc em còn nhìn nhầm sang `HKEY_LOCAL_MACHINE\SYSTEM` của máy VM đang chạy Autopsy, nên ra sai card:

```text
Intel(R) 82574L Gigabit Network Connection
```

đây không phải đáp án vì nó là thông tin của VM, không phải của disk image.

sau đó em chuyển qua output RegRipper mà Autopsy đã tạo sẵn từ hive `SOFTWARE` của image:

```text
Case Files\ModuleOutput\RecentActivity\reg\SOFTWARE-regripper-179465-full
```

trong file này em tìm section:

```text
networkcards
Microsoft\Windows NT\CurrentVersion\NetworkCards
```

ở đây thấy tên card mạng:

```text
Intel(R) PRO/1000 MT Desktop Adapter
```

<img width="1522" height="812" alt="image" src="ảnh_câu_7_của_em" />

đáp án:

```text
Intel(R) PRO/1000 MT Desktop Adapter
```


