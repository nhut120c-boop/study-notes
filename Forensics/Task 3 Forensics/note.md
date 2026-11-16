## Dump Ram
**cách 1 lấy ram máy thật**

công cụ ```ftk imager bản portable```

bước 1 chép công cụ vào một usb sạch

bước 2 cắm usb vào máy đang cần lấy ram

bước 3 chuột phải vào file ftk imager.exe chọn run as administrator

bước 4 trên thanh công cụ nhấn file chọn capture memory

bước 5 ở dòng destination path nhấn browse và chọn đường dẫn lưu vào usb

bước 6 tích chọn include pagefile nếu muốn lấy thêm bộ nhớ ảo

bước 7 nhấn capture đợi chạy xong là có file mem

**cách 2 lấy ram máy ảo**

*với vmware*

bước 1 lúc máy ảo đang chạy nhấn nút suspend để tạm dừng

bước 2 mở thư mục chứa máy ảo trên máy thật

bước 3 tìm copy file có đuôi .vmem ra chỗ khác

file .vmem chính là file ram của máy ảo

*với virtualbox*

bước 1 mở cmd trên máy thật

bước 2 gõ lệnh vboxmanage debugvm "tên_máy_ảo" dumpguestcore --filename ramdump.elf

bước 3 lấy file ramdump.elf vừa tạo ra để dùng

---

## Volatility là gì 

volatility là framework mã nguồn mở dùng để phân tích ảnh bộ nhớ ram trong điều tra số,
ứng phó sự cố, phân tích malware và nghiên cứu hệ điều hành

volatility không phải công cụ thu thập RAM, nó chỉ đọc và  phân tích file dump có sẵn để tìm thông tin cần thiết

```volatility2``` là thế hệ cũ với mô hình profile, address space, overlay và plugin dựa nhiều vào
python2

```volatility3``` là bản viết lại với context, symbol table, translation layer, template, object,
automagic, requirements và treegrid

**1. memory forensics và cách volatility nhìn ảnh nhớ**

*ram không phải là ổ đĩa*

ổ đĩa thường lưu file và metadata theo cấu trúc tương đối ổn định, còn ram chứa trạng thái
đang chạy của hệ điều hành và ứng dụng tại một thời điểm

ram có thể chứa ```object chưa từng được ghi đầy đủ``` xuống ổ đĩa như ```token, command line,
socket, khóa mã hóa, vùng code đã giải mã và dữ liệu tạm```

```RAM```có thể thiếu dữ liệu do ```swap```, ```vùng nhớ không được thu thập```, ```nén bộ nhớ```, ```lỗi đọc``` hoặc ```memory image không đầy đủ```. Vì vậy, không tìm thấy **artifact không có nghĩa là nó chưa từng tồn tại** 

plugin thường làm việc với **`virtual address`** do kernel hoặc process sử dụng, nhưng dữ liệu trong memory dump lại nằm ở **`physical address`** hoặc **`file offset`**. vì vậy framework phải dịch địa chỉ theo chuỗi:

**`virtual address` → `page table/dtb` → `physical address` → `file offset` → byte trong memory dump**

nếu một bước dịch bị sai, plugin có thể báo lỗi, trả output rỗng, hoặc nguy hiểm hơn là trả về dữ liệu có vẻ hợp lệ nhưng thực tế bị đọc hoặc diễn giải sai.

**list và scan**

**list plugin** thường đi theo **linked list** hoặc các **cấu trúc quản lý chuẩn của kernel** để lấy ra các object mà hệ điều hành đang quản lý. Có thể hình dung giống như xem **mục lục của một cuốn sách**: plugin dựa vào "danh sách" có sẵn để tìm process hoặc object

**scan plugin** thì không phụ thuộc hoàn toàn vào linked list đó mà **quét các vùng nhớ** để tìm **chữ ký, header hoặc object có hình dạng phù hợp**. Có thể hình dung như tự lật các trang của cả cuốn sách để tìm nội dung, thay vì chỉ xem mục lục

**malware có thể unlink object khỏi linked list**, làm cho object không còn xuất hiện trong danh sách quản lý của kernel nhưng dữ liệu của object chưa chắc bị xóa ngay khỏi RAM. Vì vậy cùng một memory dump, **list và scan có thể cho kết quả khác nhau**

Cách làm hợp lý là dùng **nhiều góc nhìn để đối chiếu** như `pslist`, `psscan`, `pstree`, `netscan`, `malfind`, **module list**, **handle** và **command line**. Sau đó kết hợp với **dữ liệu trên ổ đĩa và log** để xem các dấu vết có khớp với nhau hay không.

**giữ nguyên hiện trạng và kiểm chứng memory dump**

Trước khi phân tích cần ghi lại **hash, kích thước file, thời điểm thu thập, nguồn ảnh, công cụ thu thập, phiên bản framework, phiên bản Python và command line** để có thể kiểm tra lại nguồn gốc và tính toàn vẹn của memory dump.

Nên dùng **bản sao làm việc** để phân tích thay vì chỉnh sửa trực tiếp file gốc. Nếu cần thao tác hoặc chuyển đổi dữ liệu thì giữ lại file gốc để đối chiếu.

Có thể kiểm tra nhanh bằng các lệnh:

```
sha256sum memory.raw
stat memory.raw
file memory.raw
```

--- 
## Volatility2

luồng cơ bản của volatility có thể mô tả như sau

<img width="2048" height="768" alt="image" src="https://github.com/user-attachments/assets/ca753713-c184-4062-8760-6610aaaf2f38" />


tham số ```-f``` trỏ tới ảnh nhớ, ```--profile``` chọn mô tả hệ điều hành và plugin quyết định cách
truy vấn object
```
python2 vol.py -f memory.raw --profile=win7sp1x64 pslist
```
trong volatility, nhiều option được quản lý qua cấu hình chung hoặc được plugin đăng ký
riêng, nên cách đặt tên và cách xuất file có thể không đồng nhất

**1. profile**

profile là gói thông tin mô tả cấu trúc và symbol của hệ điều hành mục tiêu

profile cần biết các thông tin như tên type, offset của field, kích thước object, kiểu dữ liệu,
constant, kiến trúc và một số overlay đặc thù

ví dụ plugin muốn đọc tên image của một process cần biết vị trí của field ImageFileName
trong cấu trúc _EPROCESS

nếu offset của profile không khớp kernel trong ảnh, việc đọc field sẽ sai dù file ảnh vẫn đọc
được

profile thường được chọn qua **```imageinfo```** ,**```kdbgscan```** hoặc hiểu biết từ hệ thống thu thập

```
python2 vol.py --info | less
python2 vol.py -f memory.raw imageinfo
python2 vol.py -f memory.raw kdbgscan
```

```imageinfo``` đưa ra gợi ý, không phải bằng chứng tuyệt đối rằng profile đầu tiên là đúng
cần xác nhận bằng kiến trúc  bit hoặc  bit, kernel debugger block, thời gian khởi động,
tiến trình hệ thống, module và kết quả của nhiều plugin

**2. address space stack**

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/bf585123-784e-4a2e-b4f9-88c7617fd3ab" />


fileaddressspace đọc byte từ file

lớp **crash dump** hoặc **hibernation** hiểu header và cách bố trí page của định dạng 

lớp **paged memory** đọc page table và dịch **virtual address** sang **physical offset**

plugin chỉ gọi **read , zread , is_valid_address , vtop hoặc các helper** cao hơn mà không cần

tự biết raw dump khác crash dump ra sao

trong volshell có thể xem stack

```
>>> addrspace()
>>> addrspace().base
>>> addrspace().base.base
```
một ```address space``` thường có các thuộc tính như ```base``` , ```profile``` , ```name``` , ```order``` , ```dtb``` và ```pae```

```dtb``` hoặc ```cr``` là giá trị dùng để bắt đầu dịch page table của một không gian địa chỉ

```vtop``` chuyển ```virtual address``` thành ```physical offset``` nếu mapping tồn tại

```read``` có thể trả ít byte hơn yêu cầu khi vùng dữ liệu bị thiếu

```zread``` thường điền số không vào phần không đọc được để giữ độ dài yêu cầu trong một số
trường hợp paging

**5.object model và overlay**

plugin volatility thường lấy object từ profile bằng các lớp như ```_EPROCESS``` , ```_FILE_OBJECT``` ,
```_DRIVER_OBJECT``` hoặc type kernel tương ứng

```overlay``` cho phép thêm method, sửa cách diễn giải field hoặc gắn logic đặc thù vào type
mô hình này mạnh nhưng có thể khó theo dõi vì ```object proxy``` không phải luôn là kiểu
python nguyên bản

trong một số trường hợp thứ tự toán tử trên proxy object có thể ảnh hưởng kết quả, ví dụ
phép cộng giữa object và số có thể không tương đương với phép cộng theo thứ tự ngược lại

trong một số trường hợp thứ tự toán tử trên proxy object có thể ảnh hưởng kết quả, ví dụ
phép cộng giữa object và số có thể không tương đương với phép cộng theo thứ tự ngược lại

**6. plugin volatility 2**
plugin cơ bản gồm 

|tên plugin|chức năng|
|---|---|
|pslist| đi theo process list|
|psscan|để quét process object|
|dlllist|để xem module của process|
|netscan|để quét endpoint|
|malfind|để quét vùng có dấu hiệu inject|

## volatility 3 

Luồng cơ bản của Vocatility 3 

Tham số không còn yêu cầu --profile. Vol3 tự động nhận diện cấu trúc thông qua Symbol Tables

**cú pháp:** python3 vol.py -f 

**ví dụ:** python3 vol.py -f memory.raw windows.pslist 

**2. Translation Layers (Thay thế cho Address Space Stack)**

thay vì khái niệm address space stack, vol3 sử dụng kiến trúc layers chặt chẽ hơn:

file player: đọc byte thô từ file dump 

translation layers: đảm nhận việc phân tích page table, tự động dịch virtual address sang physical address 

các lớp này tự động nhận diện định dạng file raw crash dump..... mà plugin không cần phải quan tâm. plugin ở tầng trên cùng chỉ cần tương tác trực tiếp với không gian bộ nhớ ảo thông qua các hàm đọc chuẩn, framework sẽ tự động đi xuống các layer dưới để xử lí và xử lí luôn các trường hợp page bị swap ( paged out ) nếu có pagefile đính kèm 

**3. Oject Model và Templates**

plugin vol3 lấy object từ symbol table thông qua các VTypes và Templates

việc ép kiểu dữ liệu thô thành các object python như _EPROCESS linh hoạt nhưng an toàn hơn. các overlay rườm rà của vol2 được thay thế bằng các method xây dựng sẵn gắn trực tiếp vào templates của cấu trúc đó trong JSON 

object proxy trong vol3 được tối ưu cho python3 vấn đề sai lệch kết quả do thứ tự toán tử trên proxy object ở Vol2 đã được khắc phục phần lớn nhờ vào hệ thống typing chặt chẽ hơn

**4. Các Plugin cơ bản trong Volatility 3**

các plugin nay bắt buộc phải có tiền tố hệ điều hành mac, windows, linux...

|tên plugin |chức năng|
|---|---|
|windows.plist|liệt kê tiến trình đang chạy công khai|
|windows psscan|tìm tẩt cả cac tiến trình đã bị hacker ẩn hoặc vừa mới tắt|
|windows.dlllist|xem các file thư viện .dll mà một phần mềm đang dùng|
|windows.netscan|xem lịch sử kết nói mạng, địa chỉ IP...|
|wwindows.malfind|tìm các đoạn mã độc trong bộ nhớ của một phần mèm sạch|

## build profile cho linux memory

**build profile** là quá trình tạo ra thông tin mô tả cấu trúc dữ liệu, offset và symbol của linux kernel để volatility đọc được các object trong file ram dump

linux có nhiều phiên bản kernel khác nhau nên cấu trúc dữ liệu bên trong kernel có thể thay đổi theo phiên bản hoặc cách biên dịch

ví dụ plugin `pslist` muốn đọc danh sách process thì phải biết cấu trúc `task_struct` và vị trí các field như `pid`, `comm`, `tasks`

nếu profile không khớp với kernel của memory dump thì volatility có thể đọc sai offset, không tìm được process hoặc trả về kết quả sai

**phân biệt vol2 và vol3**

- `volatility2` sử dụng profile dạng `.zip` chứa `module.dwarf` và `System.map`
- `volatility3` sử dụng symbol table dạng `.json` hoặc `.json.xz` được tạo bằng `dwarf2json`

### 1. các thành phần cần biết

| thành phần | chức năng |
|---|---|
| `uname -r` | kiểm tra phiên bản kernel đang chạy |
| `linux-headers` | chứa các header phục vụ biên dịch module kernel |
| `DWARF` | thông tin debug mô tả cấu trúc, kiểu dữ liệu và offset |
| `module.dwarf` | file chứa thông tin cấu trúc kernel dùng để build profile vol2 |
| `System.map` | bảng ánh xạ tên symbol với địa chỉ trong kernel |
| `vmlinux` | file ELF của kernel, bản debug có thể chứa thông tin DWARF |
| `vmlinuz` | file kernel đã nén thường dùng khi boot |
| `dwarf2json` | công cụ chuyển thông tin DWARF thành ISF JSON |
| `ISF` | intermediate symbol format dùng trong vol3 |
| `kernel banner` | chuỗi nhận diện phiên bản và bản build kernel |

**lưu ý**

profile phải khớp với kernel của máy được lấy ram chứ không phải kernel của máy đang chạy volatility

---

## build linux profile cho volatility2

volatility2 sử dụng profile để xác định cấu trúc dữ liệu và symbol của linux kernel

profile thường được đóng gói thành file `.zip` gồm 2 thành phần chính

- `module.dwarf` chứa thông tin cấu trúc và kiểu dữ liệu của kernel
- `System.map` chứa tên symbol và địa chỉ tương ứng

**1. kiểm tra phiên bản kernel**

bước 1 mở terminal trên máy linux có kernel tương ứng với máy cần phân tích

bước 2 kiểm tra phiên bản kernel

```bash
uname -a
uname -r
```

ví dụ kết quả

```text
5.4.0-42-generic
```

cần ghi lại phiên bản kernel để tìm đúng `linux-headers` và `System.map`

**2. cài các công cụ cần thiết**

bước 1 cập nhật danh sách package

```bash
sudo apt update
```

bước 2 cài các công cụ

```bash
sudo apt install git build-essential dwarfdump zip linux-headers-$(uname -r)
```

| công cụ | chức năng |
|---|---|
| `git` | tải source code |
| `build-essential` | cung cấp compiler và make |
| `dwarfdump` | đọc và trích xuất thông tin DWARF |
| `zip` | đóng gói profile |
| `linux-headers` | cung cấp header tương ứng với kernel |

**3. build module.dwarf**

bước 1 clone source code volatility2

```bash
git clone https://github.com/volatilityfoundation/volatility.git
```

bước 2 mở thư mục dùng để build linux profile

```bash
cd volatility/tools/linux
```

bước 3 chạy lệnh build

```bash
make
```

lệnh `make` sẽ sử dụng kernel headers và công cụ DWARF để tạo thông tin mô tả cấu trúc kernel

bước 4 kiểm tra file vừa tạo

```bash
ls -lh module.dwarf
```

nếu build thành công sẽ có file `module.dwarf`

**lưu ý** volatility2 là framework cũ nên một số kernel mới có thể không tương thích với source code hoặc công cụ build

nên sử dụng máy ảo có kernel cũ tương thích để thực hành build vol2

**4. lấy System.map**

`System.map` thường nằm trong thư mục `/boot`

bước 1 kiểm tra danh sách System.map

```bash
ls /boot/System.map-*
```

bước 2 tìm file tương ứng với kernel đang chạy

```bash
ls -l /boot/System.map-$(uname -r)
```

ví dụ kết quả

```text
/boot/System.map-5.4.0-42-generic
```

**không được dùng System.map của kernel khác** vì địa chỉ symbol và cấu trúc kernel có thể không khớp

**5. đóng gói linux profile**

sau khi có `module.dwarf` và `System.map` thì đóng gói thành file ZIP

bước 1 chạy lệnh

```bash
zip UbuntuProfile.zip module.dwarf /boot/System.map-$(uname -r)
```

bước 2 kiểm tra các file bên trong

```bash
unzip -l UbuntuProfile.zip
```

kết quả sẽ có file `UbuntuProfile.zip` chứa `module.dwarf` và `System.map`

file ZIP này chính là linux profile dùng cho volatility2

**6. thêm profile vào volatility2**

bước 1 copy file `UbuntuProfile.zip` vào thư mục

```text
volatility/plugins/overlays/linux/
```

bước 2 quay lại thư mục chứa `vol.py`

bước 3 kiểm tra volatility đã nhận profile chưa

```bash
python2 vol.py --info | grep Linux
```

ví dụ tên profile

```text
LinuxUbuntuProfilex64
```

tên profile thực tế phụ thuộc vào tên file ZIP và kiến trúc kernel

**7. dùng profile để phân tích ram**

bước 1 chuẩn bị file ram dump của linux

ví dụ `memory.lime`

bước 2 dùng plugin `linux_pslist` để liệt kê process

```bash
python2 vol.py -f memory.lime --profile=LinuxUbuntuProfilex64 linux_pslist
```

bước 3 kiểm tra thêm các plugin khác

**xem cây process**

```bash
python2 vol.py -f memory.lime --profile=LinuxUbuntuProfilex64 linux_pstree
```

**xem kernel module**

```bash
python2 vol.py -f memory.lime --profile=LinuxUbuntuProfilex64 linux_lsmod
```

| plugin | chức năng |
|---|---|
| `linux_pslist` | liệt kê process theo danh sách của kernel |
| `linux_pstree` | xem quan hệ cha con giữa các process |
| `linux_lsmod` | liệt kê kernel module |

nếu profile khớp với kernel và file ram dump được hỗ trợ thì volatility có thể đọc các cấu trúc kernel để trả về thông tin

---

## build linux symbol table cho volatility3

volatility3 không sử dụng profile ZIP giống volatility2 mà sử dụng **symbol table** có định dạng `ISF JSON`

để build ISF thường sử dụng công cụ `dwarf2json`

**luồng build**

```text
memory dump
    |
    v
xác định kernel banner
    |
    v
tìm debug kernel tương ứng
    |
    v
dwarf2json
    |
    v
ISF JSON
    |
    v
thêm vào volatility3
    |
    v
phân tích memory dump
```

**1. xác định kernel của memory dump**

bước 1 mở terminal tại thư mục volatility3

bước 2 chạy plugin `banners`

```bash
python3 vol.py -f memory.lime banners
```

plugin sẽ quét memory dump để tìm chuỗi nhận diện kernel

ví dụ

```text
Linux version 5.4.0-42-generic ...
```

cần xác định chính xác bản build kernel vì 2 máy có cùng phiên bản kernel chưa chắc có cấu trúc và symbol giống nhau

**2. tìm debug kernel phù hợp**

để tạo ISF cần có file `vmlinux` chứa thông tin DWARF

trên Ubuntu file debug kernel thường có đường dẫn

```text
/usr/lib/debug/boot/vmlinux-<kernel-version>
```

bước 1 kiểm tra thư mục chứa debug kernel

```bash
ls /usr/lib/debug/boot/
```

bước 2 nếu không có file phù hợp thì cần tìm và cài đúng gói debug symbols của kernel mục tiêu

ví dụ file cần tìm

```text
/usr/lib/debug/boot/vmlinux-5.4.0-42-generic
```

**phân biệt vmlinux và vmlinuz**

| file | chức năng |
|---|---|
| `vmlinux` | file ELF của kernel, bản debug có thể chứa DWARF |
| `vmlinuz` | kernel image đã nén dùng để boot |
| `System.map` | chứa thông tin tên symbol và địa chỉ |
| `DWARF` | chứa thông tin kiểu dữ liệu, cấu trúc và offset |

**lưu ý** không phải file `vmlinux` nào cũng có đủ DWARF nên cần tìm đúng debug kernel

**3. cài dwarf2json**

bước 1 cài các công cụ cần thiết

```bash
sudo apt update
sudo apt install git golang-go
```

cần kiểm tra phiên bản Go có đáp ứng yêu cầu của dwarf2json hay không

bước 2 clone source code

```bash
git clone https://github.com/volatilityfoundation/dwarf2json.git
```

bước 3 mở thư mục

```bash
cd dwarf2json
```

bước 4 build công cụ

```bash
go build
```

bước 5 kiểm tra

```bash
./dwarf2json --help
```

nếu chạy thành công sẽ hiển thị các command và tham số của công cụ

**4. tạo ISF JSON**

sau khi có debug kernel thì dùng `dwarf2json` để chuyển thông tin DWARF thành JSON

bước 1 xác định đường dẫn file `vmlinux`

ví dụ

```text
/usr/lib/debug/boot/vmlinux-5.4.0-42-generic
```

bước 2 chạy lệnh

```bash
./dwarf2json linux \
--elf /usr/lib/debug/boot/vmlinux-5.4.0-42-generic \
> ubuntu-5.4.0-42.json
```

giải thích

- `linux` chọn hệ điều hành linux
- `--elf` chỉ định file ELF chứa DWARF
- `>` chuyển kết quả ra file JSON
- `ubuntu-5.4.0-42.json` là file ISF được tạo

nếu có `System.map` tương ứng thì có thể thêm vào

```bash
./dwarf2json linux \
--elf /usr/lib/debug/boot/vmlinux-5.4.0-42-generic \
--system-map /boot/System.map-5.4.0-42-generic \
> ubuntu-5.4.0-42.json
```

`--system-map` bổ sung thông tin symbol để tạo ISF đầy đủ hơn

**5. kiểm tra ISF**

bước 1 kiểm tra file đã tạo

```bash
ls -lh ubuntu-5.4.0-42.json
```

bước 2 kiểm tra cú pháp JSON

```bash
python3 -m json.tool ubuntu-5.4.0-42.json > /dev/null
```

nếu không có lỗi thì file JSON hợp lệ về cú pháp

tuy nhiên file JSON hợp lệ chưa chắc đã khớp với kernel trong memory dump

**6. thêm ISF vào volatility3**

bước 1 xác định thư mục symbol của volatility3

nếu cài bằng cách clone repository thì thư mục thường có dạng

```text
volatility3/volatility3/symbols/linux/
```

bước 2 copy file ISF vào thư mục trên

ví dụ khi đứng ở thư mục cha của repository volatility3 và file JSON cũng nằm tại đây

```bash
mkdir -p volatility3/volatility3/symbols/linux

cp ubuntu-5.4.0-42.json volatility3/volatility3/symbols/linux/
```

đường dẫn thực tế có thể khác tùy cách cài volatility3

**7. kiểm tra volatility3 đã nhận ISF chưa**

bước 1 mở thư mục chứa `vol.py`

bước 2 chạy lệnh

```bash
python3 vol.py isfinfo
```

`isfinfo` dùng để liệt kê thông tin các ISF mà volatility3 nhận diện

cần đối chiếu kernel banner trong ISF với banner lấy từ memory dump

**8. phân tích memory dump**

bước 1 sử dụng plugin `linux.pslist`

```bash
python3 vol.py -f memory.lime linux.pslist
```

bước 2 kiểm tra thêm các plugin

**xem cây process**

```bash
python3 vol.py -f memory.lime linux.pstree
```

**xem kernel module**

```bash
python3 vol.py -f memory.lime linux.lsmod
```

| plugin | chức năng |
|---|---|
| `linux.pslist` | liệt kê process |
| `linux.pstree` | xem quan hệ cha con giữa các process |
| `linux.lsmod` | liệt kê kernel module |

volatility3 sẽ tìm symbol table phù hợp thông qua thông tin kernel và cơ chế automagic mà không cần chỉ định `--profile` như volatility2

---

## các lỗi thường gặp khi build profile

| lỗi | nguyên nhân | cách xử lý |
|---|---|---|
| không tìm thấy kernel headers | chưa cài header tương ứng | kiểm tra `/lib/modules/$(uname -r)/build` |
| `make` báo lỗi | thiếu công cụ hoặc kernel không tương thích vol2 | kiểm tra compiler, headers và phiên bản kernel |
| không có `module.dwarf` | quá trình build thất bại | kiểm tra output của `make` |
| không tìm thấy `System.map` | thiếu file tương ứng với kernel | tìm đúng System.map của kernel |
| dwarf2json báo thiếu DWARF | file ELF không có debug information | tìm đúng debug kernel |
| ISF không được nhận | sai đường dẫn, lỗi định dạng hoặc cache | kiểm tra thư mục symbol và `isfinfo` |
| kernel banner không khớp | dùng ISF của bản build kernel khác | tìm đúng debug kernel |
| plugin trả kết quả rỗng | symbol sai, dump thiếu dữ liệu hoặc plugin không hỗ trợ | kiểm tra banner, symbol và file dump |

**kiểm tra kernel headers**

```bash
ls /lib/modules/$(uname -r)/build
```

**kiểm tra kernel banner**

```bash
python3 vol.py -f memory.lime banners
```

**kiểm tra ISF**

```bash
python3 vol.py isfinfo
```

---

## so sánh build profile giữa vol2 và vol3

| tiêu chí | volatility2 | volatility3 |
|---|---|---|
| cơ chế | profile | symbol table |
| định dạng | `.zip` | `.json`, `.json.xz` |
| công cụ build | `make`, `dwarfdump` | `dwarf2json` |
| dữ liệu cần có | `module.dwarf`, `System.map` | `vmlinux` có DWARF, có thể thêm `System.map` |
| cách chọn | `--profile` | tự nhận diện symbol |
| cách kiểm tra | `--info` | `isfinfo` |
| python | python2 | python3 |
| trạng thái | legacy | đang được phát triển |

## kết luận

để build profile cho linux memory cần xác định chính xác kernel của máy đã tạo ra memory dump

**volatility2**

`kernel headers` → `make` → `module.dwarf` + `System.map` → `profile.zip` → `linux_pslist`

**volatility3**

`kernel banner` → `debug kernel` → `dwarf2json` → `ISF JSON` → `linux.pslist`

điểm quan trọng là **profile phải khớp với kernel của memory dump**

build được file ZIP hoặc JSON không có nghĩa là đã thành công hoàn toàn, cần chạy plugin và kiểm tra dữ liệu trả về có hợp lý hay không

---
# lab 0

đầu tiên em tải chall và giải nén được file `Challenge.raw`, sau đó em dùng vol3 với `windows.psscan` để xem các tiến trình trong bản dump ram

```bash
python3 ~/volatility3/vol.py -f "$HOME/src/Challenge (1)/Challenge.raw" windows.psscan
```

<img width="1122" height="806" alt="image" src="https://github.com/user-attachments/assets/3da47632-6e67-410c-bc2f-32ac3af98d94" />


em thấy `cmd.exe` có pid `2096` và được mở trước `DumpIt.exe` khoảng 30 giây, vì đề hỏi john đang làm gì lúc chụp ram nên em xem thử cậu ấy đã gõ lệnh gì trong cmd

em chuyển sang vol2 để dùng `cmdscan` vì plugin đọc lịch sử lệnh của vol3 chưa hỗ trợ bản windows 7 32-bit trong lab này

```bash
./volatility_2.6_lin64_standalone -f "$HOME/src/Challenge (1)/Challenge.raw" --profile=Win7SP1x86 cmdscan
```

trong kết quả em thấy `Cmd #0` là lệnh john dùng để chạy file `demon.py.txt` trên desktop bằng python

<img width="1002" height="548" alt="image" src="https://github.com/user-attachments/assets/cc6cfc87-68f1-483b-b439-a271d8346cc3" />


```text
C:\Python27\python.exe C:\Users\hello\Desktop\demon.py.txt
```

tiếp theo em đổi `cmdscan` thành `consoles` để xem nội dung hiển thị trong cửa sổ cmd

<img width="682" height="810" alt="image" src="https://github.com/user-attachments/assets/407ae76d-1a2e-4a9a-9118-0213d15527ba" />


```bash
./volatility_2.6_lin64_standalone -f "$HOME/src/Challenge (1)/Challenge.raw" --profile=Win7SP1x86 consoles
```

ở phần `Dump:` của `conhost.exe` pid `2104`, ngay dưới lệnh chạy python có chuỗi `335d366f5d6031767631707f` nên em thử chuyển từ hex sang byte

```bash
python3 -c 'print(bytes.fromhex("335d366f5d6031767631707f"))'
```

kết quả là `b'3]6o]`1vv1p\x7f'` và em chưa đọc ra nội dung, em quay lại đề thấy tác giả nhấn mạnh chữ **môi trường** và nhắc john dùng nhiều **biến**, nên em thử dùng `envars` để xem các biến môi trường trong ram

```bash
./volatility_2.6_lin64_standalone -f "$HOME/src/Challenge (1)/Challenge.raw" --profile=Win7SP1x86 envars
```

trong output của `cmd.exe` pid `2096`, em thấy biến `Thanos` có giá trị `xor and password`, em xử lý phần `xor` trước bằng cách thử đủ 256 khóa xor một byte

<img width="687" height="152" alt="image" src="https://github.com/user-attachments/assets/b9522182-e13d-43b0-be43-baadd0f45746" />

```python
ciphertext = bytes.fromhex("335d366f5d6031767631707f")

for key in range(256):
    plaintext = bytes(byte ^ key for byte in ciphertext)
    if all(32 <= byte <= 126 for byte in plaintext):
        print(f"0x{key:02x}  {plaintext.decode('ascii')}")
```

với khóa `0x02`, em nhận được `1_4m_b3tt3r}` có dấu `}` ở cuối nên em xác định đây là phần cuối của flag

tiếp theo với manh mối `password`, em dùng `hashdump` để lấy hash của tài khoản `hello`

```bash
./volatility_2.6_lin64_standalone -f "$HOME/src/Challenge (1)/Challenge.raw" --profile=Win7SP1x86 hashdump
```

em lấy trường thứ tư trên dòng `hello` và được hash nt là `101da33f44e92c27835e64322d72e8b7`

<img width="701" height="165" alt="image" src="https://github.com/user-attachments/assets/f5da973a-9403-4117-b440-f834f4d62f73" />

em thử tra hash này trên crackstation nhưng trang báo `Not found`, trong writeup gốc tác giả tra online và lấy được phần đầu là `flag{you_are_good_but`, nên em dùng chuỗi đó để tính lại hash và đối chiếu với kết quả từ ram

```bash
printf '%s' 'flag{you_are_good_but' |
  iconv -f UTF-8 -t UTF-16LE |
  openssl dgst -provider legacy -md4
```

hash tính lại khớp với hash của `hello`, em ghép hai phần lại và được flag

```text
flag{you_are_good_but1_4m_b3tt3r}
```
