Để thêm kiến trúc **ARM64** vào hệ thống của bạn trên Ubuntu, bạn có thể sử dụng lệnh sau:

1. **Thêm kiến trúc ARM64**:

   Chạy lệnh này để thêm kiến trúc ARM64 vào hệ thống:

   ```bash
   sudo dpkg --add-architecture arm64
   ```

2. **Cập nhật danh sách gói**:

   Sau khi thêm kiến trúc, bạn cần cập nhật danh sách gói để apt có thể lấy thông tin về các gói ARM64 từ các nguồn:

   ```bash
   sudo apt update
   ```

3. **Cấu hình nguồn cho ARM64**:

   Nếu cần, bạn cũng có thể thêm các kho lưu trữ tương ứng cho kiến trúc ARM64 trong tệp `/etc/apt/sources.list` hoặc trong tệp riêng ở `/etc/apt/sources.list.d/`. Ví dụ, bạn có thể thêm dòng này nếu chưa có:

   ```bash
   deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports noble main universe
   deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports noble-updates main universe
   deb [arch=arm64] http://ports.ubuntu.com/ubuntu-ports noble-security main universe
   ```

4. **Cài đặt gói cho ARM64**:

   Sau khi cập nhật, bạn có thể cài đặt các gói cho ARM64, ví dụ:

   ```bash
   sudo apt install libssl-dev:arm64
   ```

5. **Kiểm tra kiến trúc đã thêm**:

   Bạn có thể kiểm tra xem kiến trúc ARM64 đã được thêm thành công chưa bằng lệnh:

   ```bash
   dpkg --print-foreign-architectures
   ```

Điều này sẽ đảm bảo rằng hệ thống của bạn có thể quản lý các gói cho kiến trúc ARM64. Nếu có bất kỳ vấn đề gì trong quá trình này, bạn có thể kiểm tra xem đã cấu hình đúng nguồn hay chưa, hoặc có thể cần kiểm tra lại kết nối mạng khi thực hiện `apt update`.

---

```bash
sudo dpkg --remove-architecture arm64
```

---

Nếu bạn đã cài đặt QEMU và vẫn gặp lỗi, bạn có thể thử thêm kiến trúc AArch64 vào hệ thống của bạn bằng cách sử dụng `dpkg` để cài đặt các gói AArch64. Hãy làm theo các bước sau:

1. **Thêm kiến trúc AArch64 vào hệ thống**:
   Chạy lệnh sau để thêm hỗ trợ cho kiến trúc AArch64:

   ```bash
   sudo dpkg --add-architecture arm64
   ```

2. **Cập nhật danh sách gói**:
   Sau khi thêm kiến trúc, bạn cần cập nhật danh sách các gói:

   ```bash
   sudo apt update
   ```

3. **Cài đặt các thư viện cần thiết cho AArch64**:
   Cài đặt gói `libc6` và các thư viện cần thiết khác cho AArch64:

   ```bash
   sudo apt install libc6:arm64
   ```

4. **Kiểm tra lại thư viện bị thiếu**:
   Nếu vẫn thiếu thư viện `ld-linux-aarch64.so.1`, bạn có thể tìm kiếm và cài đặt nó:

   ```bash
   sudo apt install libstdc++6:arm64
   ```

   Thư viện này thường sẽ chứa file `ld-linux-aarch64.so.1`.

Sau khi hoàn tất, thử chạy lại chương trình của bạn xem có còn báo lỗi nữa không. Nếu vẫn có lỗi, hãy cho tôi biết thêm chi tiết để tiếp tục hỗ trợ!
