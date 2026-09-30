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
| **HTYPE (High)** | **HTYPE (Low)** | **PTYPE (High)** | **PTYPE (Low)** | Offset 0 - 3 |
| **HLEN (0x06)** | **PLEN (0x04)** | **OPER (High)** | **OPER (Low)** | Offset 4 - 7 |
| **SHA Byte 0** | **SHA Byte 1** | **SHA Byte 2** | **SHA Byte 3** | Offset 8 - 11 |
| **SHA Byte 4** | **SHA Byte 5** | **SPA Byte 0** | **SPA Byte 1** | Offset 12 - 15 |
| **SPA Byte 2** | **SPA Byte 3** | **THA Byte 0** | **THA Byte 1** | Offset 16 - 19 |
| **THA Byte 2** | **THA Byte 3** | **THA Byte 4** | **THA Byte 5** | Offset 20 - 23 |
| **TPA Byte 0** | **TPA Byte 1** | **TPA Byte 2** | **TPA Byte 3** | Offset 24 - 27 |

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


