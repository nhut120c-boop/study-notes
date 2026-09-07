## Dump Ram
cách 1 lấy ram máy thật

công cụ ftk imager bản portable

bước 1 chép công cụ vào một usb sạch

bước 2 cắm usb vào máy đang cần lấy ram

bước 3 chuột phải vào file ftk imager.exe chọn run as administrator

bước 4 trên thanh công cụ nhấn file chọn capture memory

bước 5 ở dòng destination path nhấn browse và chọn đường dẫn lưu vào usb

bước 6 tích chọn include pagefile nếu muốn lấy thêm bộ nhớ ảo

bước 7 nhấn capture đợi chạy xong là có file mem

cách 2 lấy ram máy ảo

với vmware

bước 1 lúc máy ảo đang chạy nhấn nút suspend để tạm dừng

bước 2 mở thư mục chứa máy ảo trên máy thật

bước 3 tìm copy file có đuôi .vmem ra chỗ khác

file .vmem chính là file ram của máy ảo

với virtualbox

bước 1 mở cmd trên máy thật

bước 2 gõ lệnh vboxmanage debugvm "tên_máy_ảo" dumpguestcore --filename ramdump.elf

bước 3 lấy file ramdump.elf vừa tạo ra để dùng

---

## Volatility là gì 

volatility là framework mã nguồn mở dùng để phân tích ảnh bộ nhớ ram trong điều tra số,
ứng phó sự cố, phân tích malware và nghiên cứu hệ điều hành

volatility không phải công cụ thu thập RAM, nó chỉ đọc và  phân tích file dump có sẵn để tìm thông tin cần thiết



