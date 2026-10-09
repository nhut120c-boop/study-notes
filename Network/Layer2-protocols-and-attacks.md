## Chương 1: Cơ chế bit-level của bản tin ARP & 3 loại bản tin 

**1.ARP là gì**

  ARP hoạt động giữa layer 2 và layer 3, gói tin arp được đặt trực tiếp bên trong payload của khung EThenet II
  
 
| Destination MAC   | Source MAC        | EtherType         | ARP Payload                 |
|---|---|---|---|
| (6 bytes)         | (6 bytes)         | (2 bytes: 0x0806) | (28 bytes)                  |
  
ethertype luôn cố định là 0x0806 để báo hiệu cho card mạng biết là đây là gói ARP

**2. Cấu trúc của 28 byte trong ARP**

| Bit 0-7 | Bit 8-15 | Bit 16-23 | Bit 24-31 | Offset |
| :--- | :--- | :--- | :--- | :--- |
| HTYPE (High) | HTYPE (Low) | PTYPE (High) | PTYPE (Low) | Offset 0 - 3 |
| HLEN (0x06) | PLEN (0x04) | OPER (High) | OPER (Low) | Offset 4 - 7 |
| SHA Byte 0 | SHA Byte 1 | SHA Byte 2 | SHA Byte 3 | Offset 8 - 11 |
| SHA Byte 4 | SHA Byte 5 | SPA Byte 0 | SPA Byte 1 | Offset 12 - 15 |
| SPA Byte 2 | SPA Byte 3 | THA Byte 0 | THA Byte 1 | Offset 16 - 19 |
| THA Byte 2 | THA Byte 3 | THA Byte 4 | THA Byte 5 | Offset 20 - 23 |
| TPA Byte 0 | TPA Byte 1 | TPA Byte 2 | TPA Byte 3 | Offset 24 - 27 |

HTYPE: khai báo loại môi trường mạng vật lí đang truyền tải

ví dụ: 0x0001 ( ethernet)

PTYPE: khai báo loại giao thức mạng 

ví dụ: 0x0800 giao thức IPv4

HLEN: xác định độ dài bằng byte của địa chỉ vật lí

PLEN: xác định độ dài của địa chỉ IP 

OPER: xác định hành động, 1 là gọi, 2 là trả lời 

SHA: lưu địa chỉ mac của thiết bị gửi 

SPA: lưu địa chỉ IP của địa chỉ gửi 

THA: lưu địa chỉ mac của máy đích, chưa biết nó điền là 00:00:00:00:00 

TPA: lưu địa chỉ IP đích 

**3. Phân tích 3 loại tin ARP**

*ARP Request*

mục đích: tìm địa chỉ mac của thiết bị đích khi chỉ biết IP 

chế độ truyền: broadcast ( địa chỉ là ff:ff:ff:ff:ff:ff) để mọi thiết bị cùng subnet đều nhận được gói tin này 

mã vận hành: 00x0001 

trường target mang giá trị 00:00:00:00:00:00 vì chưa biết được địa chỉ mac 

trường ip target mang giá trị IP mục tiêu cần phân giải 

*ARP Reply*

là gói tin trả lời lại cho gói tin ARP request, cung cấp địa chỉ mac mà gói request yêu cầu 

Chế độ truyền: Unicast, gói này được gửi đích dang đến địa chỉ mac của thiết bị vừa gửi arp request, không phát toàn mạng

mã vận hành 00x0002

xử lí dữ liệu: đảo ngược thông tin gói request 

SHA thay bằng mac chính nó

SPA thay bằng IP chính nó

THA thay bằng SHA gói arp request

TPA thay bằng SPA gói arp request 

*Gratuitous ARP*

định nghĩa: là gói tin đặc biệt, do thiết bị tự động gửi broadcast để công bố ip và mac của mình mà không cần thiết bị nào hỏi 

--ai hỏi mà bộ trưởng trả lời--

đặc điểm: trong payload 28bytes, trường ip người gửi và ip mục tiêu hoàn toàn giống nhau 

GARP để thông báo chứ không dùng để tìm kiếm

<img width="1091" height="376" alt="image" src="https://github.com/user-attachments/assets/49cb83c7-37a1-4ed7-b730-b485f31b1af7" />

đây là cấu trúc của một gói tin ARP 

Request payload gồm 

<img width="1148" height="548" alt="image" src="https://github.com/user-attachments/assets/993a9956-9663-470c-86ca-40c1d8a6d447" />

Reply payload gồm 

<img width="1121" height="500" alt="image" src="https://github.com/user-attachments/assets/86a51563-bfcc-4fd8-b3e8-1e613e00af1e" />

link demo 

```
https://networdzerod.tiiny.site
```

gói tin demo phân tích 

```
https://mega.nz/file/66JjTThB#9LZSrVbRnM4pjzFcI_9RXEZW0DsIJmalZMKb8by8OP4
```

## Chương 2: Khai thác & điều tra tấn công ARP POISONING 

**1. điểm yếu gốc của giao  thức ARP** 

không lưu trạng thái: thiết bị sẵn sàng cập nhật bảng ARP cache khi nhận được một bản tin ARP Reply, ngay cả khi nó không hề gửi ARP request nào trước đó 

không có cơ chế xác thực: không có chữ kí số hay mật mã để chứng minh thiết bị thực sự sữu hữu ip mà request đã báo 

**Mình sẽ dựng 2 lab với mô hình 1 attack 1 victim để minh họa:**

*Bước 1:* chuẩn bị 1 máy attack 

<img width="1294" height="909" alt="image" src="https://github.com/user-attachments/assets/e5c6fdb7-5511-40db-a6a1-940e3023b4f8" />

*Bước 2:* chuẩn bị máy victim 

<img width="1142" height="930" alt="image" src="https://github.com/user-attachments/assets/e54c5f69-bd4e-4eca-8750-22bd0bb3c9fb" />

*Bước 3:* cấu hình môi trường mạng cả 2 VM

<img width="766" height="509" alt="image" src="https://github.com/user-attachments/assets/fed21d26-c687-400b-b1ae-c7860fc57faf" />


<img width="774" height="515" alt="image" src="https://github.com/user-attachments/assets/25036447-3f30-4ea2-a5dd-3d4915eca8a2" />

*Bước 4:* thu thập thông số định tuyến

Trên Kali Linux, kẻ tấn công gõ lệnh ifconfig để xem IP của chính mình  là 192.168.110.6, từ đó suy ra dải mạng cục bộ cần quét là 192.168.110.0/24

<img width="671" height="560" alt="image" src="https://github.com/user-attachments/assets/ac0dcaaf-1846-478b-9e62-74f282a051de" />

sau đó attacker tiến hành scan all mạng bằng tool arp-scan


<img width="828" height="614" alt="image" src="https://github.com/user-attachments/assets/a21951bc-92d4-4f59-b0d4-67062abf2ddc" />


từ kết quả trả về, attacker có được danh sách chi tiết các địa chỉ IP đi kèm với địa chỉ MAC thực. attacker chọn máy nạn nhân có địa chỉ IP 192.168.110.122, với địa chỉ MAC tương ứng được quét ra là 08:00:27:6f:a9:6a. default gateway là 192.168.110.1
Bước 5: bắt đầu tấn công 
sau khi có được ip và gateway máy nạn nhân ta bắt đầu xài tool arpspoof gửi liên tục các gói arp reply giả mạo vào mạng 

script xài trên máy attack

```
sudo arpspoof -i eth0 -t 192.168.110.122 192.168.110.1
```
<img width="852" height="464" alt="image" src="https://github.com/user-attachments/assets/c3d05f91-f34b-4f3c-92f7-1ef7af53a639" />

Bước 6: kiểm tra lại kết quả ở máy victim

mở cmd trên máy victim và gõ lệnh ```arp -a```

<img width="1043" height="615" alt="image" src="https://github.com/user-attachments/assets/539da9bc-6211-48b7-ae23-cd3f5f523abd" />

kết quả: địa chỉ MAC của Default Gateway 192.168.110.1 đã bị đổi thành địa chỉ MAC của máy attacker (08-00-27-5a-87-bc)

Bước 7 : kiểm tra hậu quả

Sau khi thành công chạy tool trên máy attack thì giờ máy victim đã hoàn toàn bị ngắt kết nối với môi trường mạng, khi ping 8.8.8.8 dễ bị timed out toàn bộ

<img width="1178" height="933" alt="Ảnh chụp màn hình 2026-10-03 133537" src="https://github.com/user-attachments/assets/0d6ae32b-e831-4ae0-ba65-036909ad92a7" />

tiếp theo là tới lab 2 

## MAN-IN-THE-MIDDLE (ARP POISONING) & SECURITY MONITORING

1. Chuẩn bị vm

 vm attack 

 <img width="938" height="627" alt="image" src="https://github.com/user-attachments/assets/9dfcd51c-2412-44ab-9af9-31ce1354716f" />

 chuẩn bị vm victim 

 <img width="890" height="641" alt="image" src="https://github.com/user-attachments/assets/e59f2863-bcd4-46c9-8fb8-d51ac64955e1" />

2. scan victim

công cụ sử dụng: apr-scan 

lệnh sử dụng
```
sudo arp-scan --interface=eth0 --localnet
```
<img width="818" height="406" alt="image" src="https://github.com/user-attachments/assets/043b01b1-b3a3-4292-bdc6-d9d3603afa02" />

thu được:

gateway: 192.168.1.1 mac: 30:40:74:a0:6c:18

victim 192.168.1.33 mac: 08:00:27:6f:a9:6a

3. bắt đầu tấn công
 trước tiên phải bật chuyển tiếp gói tin để mạng không bị nghẽn

```
sudo sysctl -w net.ipv4.ip_forward=1
```

<img width="559" height="380" alt="image" src="https://github.com/user-attachments/assets/22296159-8759-49bc-bb78-31e9ac1c1ca9" />

## thực thi arp spoofing 

mở 2 cửa sổ độc lập trên vm kali để giả mạo lừa cả 2 phía victim và gateway 

ở terminal 1: 

```
sudo arpspoof -i eth0 -t 192.168.1.33 192.168.1.1
```
lừa win kali là gateway 

<img width="925" height="626" alt="image" src="https://github.com/user-attachments/assets/6e066957-db47-4062-bbae-956b89abfc33" />


ở terminal 2: 

```
sudo arpspoof -i eth0 -t 192.168.1.1 192.168.1.33
```
<img width="960" height="642" alt="image" src="https://github.com/user-attachments/assets/06b04b50-5a0f-4c24-9689-d5883ff3f839" />



