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
| **Hardware Type (HTYPE)** | **Protocol Type (PTYPE)** | Offset 0 - 3 |
| **HLEN (0x06)** | **PLEN (0x04)** | **Operation (OPER)** | Offset 4 - 7 |
| **Sender Hardware Addr (SHA) (Bytes 0-3)** | Offset 8 - 11 |
| **SHA (Bytes 4-5)** | **Sender Protocol Addr (SPA) (Bytes 0-1)** | Offset 12 - 15 |
| **SPA (Bytes 2-3)** | **Target Hardware Addr (THA) (Bytes 0-1)** | Offset 16 - 19 |
| **Target Hardware Addr (THA) (Bytes 2-5)** | Offset 20 - 23 |
| **Target Protocol Addr (TPA)** | Offset 24 - 27 |
