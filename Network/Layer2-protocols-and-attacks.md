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

[ph_n_t_ch_g_i_tin_arp.html](https://github.com/user-attachments/files/32846539/ph_n_t_ch_g_i_tin_arp.html)

<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Công cụ Phân tích Gói tin ARP</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
        }
        .hex-font {
            font-family: 'JetBrains Mono', monospace;
        }
        /* Custom scrollbar cho khung kết quả */
        .custom-scrollbar::-webkit-scrollbar {
            width: 8px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: #f1f1f1; 
            border-radius: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #cbd5e1; 
            border-radius: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover {
            background: #94a3b8; 
        }
    </style>
</head>
<body class="min-h-screen p-4 md:p-8 flex flex-col items-center">

    <div class="max-w-4xl w-full bg-white rounded-2xl shadow-xl overflow-hidden">
        
        <!-- Header -->
        <div class="bg-blue-600 p-6 text-white text-center">
            <h1 class="text-3xl font-bold mb-2">Bộ Phân Tích Cấu Trúc ARP</h1>
            <p class="text-blue-100 text-sm md:text-base">Mổ xẻ 28 Bytes Payload theo thời gian thực</p>
        </div>

        <div class="p-6 border-b border-gray-200 bg-gray-50">
            <div class="mb-4">
                <label for="hexInput" class="block text-sm font-semibold text-gray-700 mb-2">Nhập chuỗi Hexadecimal của ARP Payload (Dài ít nhất 56 ký tự = 28 bytes):</label>
                <textarea id="hexInput" rows="3" class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500 hex-font text-sm text-gray-800 transition-colors" placeholder="Ví dụ: 0001080006040001aabbccddeeffc0a8010a000000000000c0a80105"></textarea>
            </div>
            <div class="flex flex-wrap gap-3">
                <button id="analyzeBtn" class="bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-6 rounded-lg shadow-sm transition-colors flex items-center">
                    <svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
                    Phân tích
                </button>
                <button id="sampleRequestBtn" class="bg-gray-200 hover:bg-gray-300 text-gray-700 font-medium py-2 px-4 rounded-lg shadow-sm transition-colors text-sm">
                    Tải mẫu: ARP Request
                </button>
                <button id="sampleReplyBtn" class="bg-gray-200 hover:bg-gray-300 text-gray-700 font-medium py-2 px-4 rounded-lg shadow-sm transition-colors text-sm">
                    Tải mẫu: ARP Reply
                </button>
                <button id="sampleGarpBtn" class="bg-gray-200 hover:bg-gray-300 text-gray-700 font-medium py-2 px-4 rounded-lg shadow-sm transition-colors text-sm">
                    Tải mẫu: GARP
                </button>
            </div>
            
            <div id="errorMsg" class="hidden mt-3 text-red-600 text-sm font-medium flex items-center">
                <svg class="w-4 h-4 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
                <span id="errorText"></span>
            </div>
        </div>

        <div id="resultContainer" class="hidden">
            
            <!-- Summary Banner -->
            <div id="packetSummary" class="bg-blue-50 border-l-4 border-blue-500 p-4 m-6 rounded-r-lg">
                <div class="flex items-start">
                    <div class="flex-shrink-0">
                        <svg class="h-6 w-6 text-blue-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                        </svg>
                    </div>
                    <div class="ml-3">
                        <h3 class="text-lg font-medium text-blue-800">Bản dịch thông điệp</h3>
                        <div class="mt-2 text-sm text-blue-700" id="humanTranslation">
                            <!-- Translation goes here -->
                        </div>
                    </div>
                </div>
            </div>

            <!-- Detailed Breakdown Grid -->
            <div class="px-6 pb-8">
                <h2 class="text-xl font-bold text-gray-800 mb-4 border-b pb-2">Cấu trúc Bit-Level (28 Bytes)</h2>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    
                    <!-- Nhóm 1: Luật chơi -->
                    <div class="bg-white border border-gray-200 rounded-xl shadow-sm overflow-hidden md:col-span-2">
                        <div class="bg-gray-100 px-4 py-2 border-b border-gray-200">
                            <h3 class="font-semibold text-gray-700 text-sm">Nhóm 1: Khai báo môi trường & Hành động (8 Bytes)</h3>
                        </div>
                        <div class="p-0">
                            <table class="min-w-full divide-y divide-gray-200 text-sm">
                                <tbody class="divide-y divide-gray-200">
                                    <tr class="hover:bg-gray-50">
                                        <td class="px-4 py-3 font-medium text-gray-900 w-1/4">HTYPE (2B)</td>
                                        <td class="px-4 py-3 hex-font text-blue-600 w-1/4" id="val_htype_hex"></td>
                                        <td class="px-4 py-3 text-gray-600" id="val_htype_desc"></td>
                                    </tr>
                                    <tr class="hover:bg-gray-50">
                                        <td class="px-4 py-3 font-medium text-gray-900">PTYPE (2B)</td>
                                        <td class="px-4 py-3 hex-font text-blue-600" id="val_ptype_hex"></td>
                                        <td class="px-4 py-3 text-gray-600" id="val_ptype_desc"></td>
                                    </tr>
                                    <tr class="hover:bg-gray-50">
                                        <td class="px-4 py-3 font-medium text-gray-900">HLEN (1B)</td>
                                        <td class="px-4 py-3 hex-font text-blue-600" id="val_hlen_hex"></td>
                                        <td class="px-4 py-3 text-gray-600" id="val_hlen_desc"></td>
                                    </tr>
                                    <tr class="hover:bg-gray-50">
                                        <td class="px-4 py-3 font-medium text-gray-900">PLEN (1B)</td>
                                        <td class="px-4 py-3 hex-font text-blue-600" id="val_plen_hex"></td>
                                        <td class="px-4 py-3 text-gray-600" id="val_plen_desc"></td>
                                    </tr>
                                    <tr class="hover:bg-blue-50 bg-blue-50/50">
                                        <td class="px-4 py-3 font-bold text-gray-900">OPER (2B)</td>
                                        <td class="px-4 py-3 hex-font font-bold text-blue-700" id="val_oper_hex"></td>
                                        <td class="px-4 py-3 font-medium text-blue-800" id="val_oper_desc"></td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>

                    <!-- Nhóm 2: Sender -->
                    <div class="bg-white border border-green-200 rounded-xl shadow-sm overflow-hidden">
                        <div class="bg-green-100 px-4 py-2 border-b border-green-200">
                            <h3 class="font-semibold text-green-800 text-sm">Nhóm 2: Thông tin Người Gửi (10 Bytes)</h3>
                        </div>
                        <div class="p-4 space-y-4">
                            <div>
                                <p class="text-xs font-semibold text-gray-500 uppercase tracking-wider mb-1">SHA (Sender MAC - 6B)</p>
                                <div class="flex items-center">
                                    <span class="hex-font text-sm bg-gray-100 text-gray-600 px-2 py-1 rounded mr-3" id="val_sha_hex"></span>
                                    <span class="font-mono font-bold text-green-700 text-lg" id="val_sha_dec"></span>
                                </div>
                            </div>
                            <div>
                                <p class="text-xs font-semibold text-gray-500 uppercase tracking-wider mb-1">SPA (Sender IP - 4B)</p>
                                <div class="flex items-center">
                                    <span class="hex-font text-sm bg-gray-100 text-gray-600 px-2 py-1 rounded mr-3" id="val_spa_hex"></span>
                                    <span class="font-mono font-bold text-green-700 text-lg" id="val_spa_dec"></span>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Nhóm 3: Target -->
                    <div class="bg-white border border-red-200 rounded-xl shadow-sm overflow-hidden">
                        <div class="bg-red-100 px-4 py-2 border-b border-red-200">
                            <h3 class="font-semibold text-red-800 text-sm">Nhóm 3: Thông tin Đích Đến (10 Bytes)</h3>
                        </div>
                        <div class="p-4 space-y-4">
                            <div>
                                <p class="text-xs font-semibold text-gray-500 uppercase tracking-wider mb-1">THA (Target MAC - 6B)</p>
                                <div class="flex items-center">
                                    <span class="hex-font text-sm bg-gray-100 text-gray-600 px-2 py-1 rounded mr-3" id="val_tha_hex"></span>
                                    <span class="font-mono font-bold text-red-700 text-lg" id="val_tha_dec"></span>
                                </div>
                                <p class="text-xs text-gray-500 mt-1 italic" id="val_tha_note"></p>
                            </div>
                            <div>
                                <p class="text-xs font-semibold text-gray-500 uppercase tracking-wider mb-1">TPA (Target IP - 4B)</p>
                                <div class="flex items-center">
                                    <span class="hex-font text-sm bg-gray-100 text-gray-600 px-2 py-1 rounded mr-3" id="val_tpa_hex"></span>
                                    <span class="font-mono font-bold text-red-700 text-lg" id="val_tpa_dec"></span>
                                </div>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
            
            <!-- Raw Payload Visualizer -->
            <div class="bg-gray-800 p-6 rounded-b-2xl">
                <h3 class="text-gray-300 font-semibold text-sm mb-3">Dải Hex Data (Click để xem tương ứng)</h3>
                <div class="font-mono text-sm break-all leading-loose text-gray-500" id="hexVisualizer">
                    <!-- Javascript will populate this with colored chunks -->
                </div>
            </div>

        </div>
    </div>

    <script>
        // --- Helper Functions ---
        
        // Chuyển chuỗi Hex IP (vd: c0a8010a) thành chuẩn IPv4 (192.168.1.10)
        function hexToIp(hexStr) {
            if (hexStr.length !== 8) return "Invalid";
            let chunks = [];
            for (let i = 0; i < 8; i += 2) {
                chunks.push(parseInt(hexStr.substr(i, 2), 16));
            }
            return chunks.join('.');
        }

        // Chuyển chuỗi Hex MAC (vd: aabbccddeeff) thành chuẩn MAC (aa:bb:cc:dd:ee:ff)
        function hexToMac(hexStr) {
            if (hexStr.length !== 12) return "Invalid";
            let chunks = [];
            for (let i = 0; i < 12; i += 2) {
                chunks.push(hexStr.substr(i, 2));
            }
            return chunks.join(':');
        }

        // Định dạng mã hex để hiển thị (thêm 0x phía trước)
        function formatHex(hexStr) {
            return "0x" + hexStr;
        }

        // Tạo thẻ span có màu cho Visualizer
        function createColorChunk(hex, colorClass, tooltip) {
            return `<span class="px-1 mx-0.5 rounded cursor-help ${colorClass}" title="${tooltip}">${hex}</span>`;
        }

        // --- Core Logic ---
        
        const hexInput = document.getElementById('hexInput');
        const analyzeBtn = document.getElementById('analyzeBtn');
        const errorMsg = document.getElementById('errorMsg');
        const errorText = document.getElementById('errorText');
        const resultContainer = document.getElementById('resultContainer');
        const hexVisualizer = document.getElementById('hexVisualizer');
        const humanTranslation = document.getElementById('humanTranslation');

        // Mẫu dữ liệu chuẩn
        // 0001 (HTYPE) 0800 (PTYPE) 06 (HLEN) 04 (PLEN) 0001 (OPER=Req) 
        // aabbccddeeff (SHA) c0a8010a (SPA=192.168.1.10) 
        // 000000000000 (THA) c0a80105 (TPA=192.168.1.5)
        const SAMPLE_REQUEST = "0001080006040001aabbccddeeffc0a8010a000000000000c0a80105";
        
        // Thay đổi OPER=0002, Đảo SHA/THA, SPA/TPA và gán MAC đích thật (112233445566)
        const SAMPLE_REPLY   = "0001080006040002112233445566c0a80105aabbccddeeffc0a8010a";
        
        // GARP: SPA = TPA (cùng 192.168.1.10)
        const SAMPLE_GARP    = "0001080006040001aabbccddeeffc0a8010a000000000000c0a8010a";

        // Bind sample buttons
        document.getElementById('sampleRequestBtn').addEventListener('click', () => { hexInput.value = SAMPLE_REQUEST; analyze(); });
        document.getElementById('sampleReplyBtn').addEventListener('click', () => { hexInput.value = SAMPLE_REPLY; analyze(); });
        document.getElementById('sampleGarpBtn').addEventListener('click', () => { hexInput.value = SAMPLE_GARP; analyze(); });

        function analyze() {
            // Chuẩn hóa input: xóa khoảng trắng, dấu hai chấm, chuyển về chữ thường
            let rawHex = hexInput.value.replace(/[\s\:\.\-]/g, '').toLowerCase();
            
            // Validate cơ bản
            if (!/^[0-9a-f]+$/.test(rawHex)) {
                showError("Chỉ chấp nhận ký tự Hexadecimal (0-9, a-f).");
                return;
            }
            if (rawHex.length < 56) {
                showError(`Gói tin quá ngắn! Cấu trúc ARP chuẩn yêu cầu 28 bytes (56 ký tự hex). Hiện tại: ${rawHex.length/2} bytes.`);
                return;
            }
            if (rawHex.length > 56) {
                 // Cắt đúng 56 ký tự đầu (28 bytes)
                 rawHex = rawHex.substring(0, 56);
            }

            hideError();
            
            try {
                // Bóc tách dữ liệu theo số byte (1 byte = 2 ký tự hex)
                const parsed = {
                    htype: rawHex.substr(0, 4),   // 2B
                    ptype: rawHex.substr(4, 4),   // 2B
                    hlen: rawHex.substr(8, 2),    // 1B
                    plen: rawHex.substr(10, 2),   // 1B
                    oper: rawHex.substr(12, 4),   // 2B
                    sha: rawHex.substr(16, 12),   // 6B
                    spa: rawHex.substr(28, 8),    // 4B
                    tha: rawHex.substr(36, 12),   // 6B
                    tpa: rawHex.substr(48, 8)     // 4B
                };

                // Đổ dữ liệu vào UI - Nhóm 1
                document.getElementById('val_htype_hex').textContent = formatHex(parsed.htype);
                document.getElementById('val_htype_desc').textContent = parsed.htype === '0001' ? 'Ethernet (Chuẩn mạng LAN)' : 'Khác';
                
                document.getElementById('val_ptype_hex').textContent = formatHex(parsed.ptype);
                document.getElementById('val_ptype_desc').textContent = parsed.ptype === '0800' ? 'IPv4 (Ánh xạ IP)' : 'Khác';
                
                document.getElementById('val_hlen_hex').textContent = formatHex(parsed.hlen);
                document.getElementById('val_hlen_desc').textContent = parseInt(parsed.hlen, 16) + ' bytes (Dành cho địa chỉ MAC)';
                
                document.getElementById('val_plen_hex').textContent = formatHex(parsed.plen);
                document.getElementById('val_plen_desc').textContent = parseInt(parsed.plen, 16) + ' bytes (Dành cho địa chỉ IPv4)';
                
                let operNum = parseInt(parsed.oper, 16);
                let operType = "Không xác định";
                let isGarp = false;
                
                document.getElementById('val_oper_hex').textContent = formatHex(parsed.oper);
                if (operNum === 1) operType = "ARP Request (Truy vấn / Đi hỏi)";
                else if (operNum === 2) operType = "ARP Reply (Phản hồi / Trả lời)";
                document.getElementById('val_oper_desc').textContent = operType;

                // Đổ dữ liệu vào UI - Nhóm 2 & 3
                const spa_dec = hexToIp(parsed.spa);
                const tpa_dec = hexToIp(parsed.tpa);
                const sha_dec = hexToMac(parsed.sha);
                const tha_dec = hexToMac(parsed.tha);

                document.getElementById('val_sha_hex').textContent = formatHex(parsed.sha);
                document.getElementById('val_sha_dec').textContent = sha_dec;
                
                document.getElementById('val_spa_hex').textContent = formatHex(parsed.spa);
                document.getElementById('val_spa_dec').textContent = spa_dec;

                document.getElementById('val_tha_hex').textContent = formatHex(parsed.tha);
                document.getElementById('val_tha_dec').textContent = tha_dec;
                
                let tha_note = "";
                if(parsed.tha === "000000000000") tha_note = "Toàn số 0 vì đây là thiết bị đang được tìm kiếm (Chưa biết MAC).";
                document.getElementById('val_tha_note').textContent = tha_note;
                
                document.getElementById('val_tpa_hex').textContent = formatHex(parsed.tpa);
                document.getElementById('val_tpa_dec').textContent = tpa_dec;

                // Kiểm tra loại bản tin
                if (parsed.spa === parsed.tpa) {
                    isGarp = true;
                    operType = "Gratuitous ARP (GARP - Tự phát)";
                    document.getElementById('val_oper_desc').textContent = operType + " (Mã gốc: Request " + operNum + ")";
                }

                // Sinh câu dịch theo ngôn ngữ con người
                let msg = "";
                if (isGarp) {
                    msg = `<p class="font-bold text-base mb-1">📢 Thông báo toàn mạng (GARP):</p>
                           <p>"Chú ý! Tôi đang sở hữu IP <strong class="text-blue-900">${spa_dec}</strong>. 
                           Hãy cập nhật địa chỉ MAC của IP này thành <strong class="text-blue-900">${sha_dec}</strong> ngay lập tức!"</p>`;
                } else if (operNum === 1) {
                    msg = `<p class="font-bold text-base mb-1">❓ Lời kêu gọi (ARP Request):</p>
                           <p>"Xin chào mọi người! Ai trong mạng đang mang địa chỉ IP <strong class="text-blue-900">${tpa_dec}</strong>? 
                           Làm ơn gửi lại địa chỉ MAC cho tôi. Tôi là <strong class="text-blue-900">${spa_dec}</strong> (MAC của tôi là ${sha_dec})."</p>`;
                } else if (operNum === 2) {
                    msg = `<p class="font-bold text-base mb-1">✅ Lời đáp trả (ARP Reply):</p>
                           <p>"Đích danh gửi <strong class="text-blue-900">${tpa_dec}</strong> (MAC: ${tha_dec}): 
                           Bạn vừa gọi tôi đúng không? Tôi mang IP <strong class="text-blue-900">${spa_dec}</strong> đây, và địa chỉ MAC chính xác của tôi là <strong class="text-green-700">${sha_dec}</strong> nhé!"</p>`;
                } else {
                    msg = "Cấu trúc gói tin không nằm trong các loại Request, Reply hoặc GARP cơ bản.";
                }
                humanTranslation.innerHTML = msg;

                // Sinh dải màu Visualizer
                let vizHtml = "";
                vizHtml += createColorChunk(parsed.htype, "bg-gray-700 text-white hover:bg-gray-600", "HTYPE (2B)");
                vizHtml += createColorChunk(parsed.ptype, "bg-gray-600 text-white hover:bg-gray-500", "PTYPE (2B)");
                vizHtml += createColorChunk(parsed.hlen, "bg-gray-500 text-white hover:bg-gray-400", "HLEN (1B)");
                vizHtml += createColorChunk(parsed.plen, "bg-gray-500 text-white hover:bg-gray-400", "PLEN (1B)");
                vizHtml += createColorChunk(parsed.oper, "bg-blue-600 text-white hover:bg-blue-500 font-bold", "OPER (2B)");
                
                vizHtml += createColorChunk(parsed.sha, "bg-green-700 text-white hover:bg-green-600", "SHA - Sender MAC (6B)");
                vizHtml += createColorChunk(parsed.spa, "bg-green-500 text-white hover:bg-green-400", "SPA - Sender IP (4B)");
                
                vizHtml += createColorChunk(parsed.tha, "bg-red-700 text-white hover:bg-red-600", "THA - Target MAC (6B)");
                vizHtml += createColorChunk(parsed.tpa, "bg-red-500 text-white hover:bg-red-400", "TPA - Target IP (4B)");

                hexVisualizer.innerHTML = vizHtml;

                // Hiển thị kết quả
                resultContainer.classList.remove('hidden');

            } catch (err) {
                showError("Lỗi khi xử lý dữ liệu. Vui lòng kiểm tra lại cấu trúc chuỗi.");
                console.error(err);
            }
        }

        function showError(msg) {
            errorText.textContent = msg;
            errorMsg.classList.remove('hidden');
            resultContainer.classList.add('hidden');
        }

        function hideError() {
            errorMsg.classList.add('hidden');
        }

        // Bind nút Phân tích
        analyzeBtn.addEventListener('click', analyze);

    </script>
</body>
</html>



