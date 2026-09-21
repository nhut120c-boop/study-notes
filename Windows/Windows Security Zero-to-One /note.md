## Windows Security, Userland, Kernel, Defender, EDR, AV, APIs, Events, and Tools

Hướng dẫn về Windows từ 0 đến 1 

Đây là 1 bài hướng dẫn về cách hoạt động của Windows và hệ thống bảo mật của nó, và các công cụ giám sát như Microsoft Defender

**Phạm vi nghiên cứu:** Windows internals, mô hình bảo mật, kiểm toán, Dữ liệu từ xa / giám sát từ xa, công cụ defender

**Đối tượng:** Người mới bắt đầu, và muốn biết các thuật ngữ thuật tế

**Tập trung:** Học tập, phát hiện và phòng thủ 

**Tài liệu không nhằm mục đích:** Tạo mã độc, tấn công....

---

## 1. What Windows is?

1.1 An operating system, concretely

Một hệ điều hành là phần mềm nằm giữa phần cứng và ứng dụng, Nó phân chia bộ nhớ, quyết định ứng dụng nào được chạy, cấp quyền được phép và không được phép cho các phần mềm khi tương tác với nhau

Windows là 1 hệ điều hành của Mirosoft, mọi phiên bản windows như 7 10 11 đều dùng chung 1 thiết kế cốt lõi là Windows NT ( NT = New Technology)

1.2 Các tếp thực thi chính tao nên Windows

Windows không phải là 1 chương trình đơn lẻ, nó tập hợp các tệp cụ thể với từng nhiệm vụ riêng 

|Tệp|Vai trò|
  |---|-------|
  |ntoskrnl.exe|Tệp thực thi của windows, phần nhân cốt lõi của hệ điều hành|
  |hal.dll|Hardware Abstraction Layer - Giúp đồng bộ phần cứng với kernel, tránh sự khác biệt|
  |ntdll.dll|giúp app và kernel trung gian tương tác,thư viện user-mode ở tầng thấp nhất; bọc các hàm Native API (Nt* / Zw*)|
  |kernel32.dll|kernel32.dll là thư viện User Mode xử lý các hàm Win32 về tệp, tiến trình và bộ nhớ, giúp chuẩn hóa yêu cầu từ ứng dụng để gửi tiếp xuống ntdll.dll|
  |user32.dll|các API về UX/UI và GUI|
  |win32k.sys|nhận yêu cầu từ user32.sys và hành động nó ở kernel|
  |lsass.exe|liên quan trực tiếp tới advapi.dll nahywmf để xác thực |
