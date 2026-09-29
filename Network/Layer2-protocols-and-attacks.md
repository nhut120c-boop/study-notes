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
