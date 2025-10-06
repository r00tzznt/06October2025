Dễ hiểu nhất: đây là **khác nhau về ABI (giao ước gọi hàm) cho số thực dấu chấm động** trên ARM.

# 1) Khái niệm float ABI

- **soft-float (soft)**: số thực **không** truyền/nhận qua thanh ghi FPU; compiler dùng thanh ghi tổng quát & thư viện phần mềm để tính toán.
- **hard-float (hard)**: số thực **truyền/nhận qua thanh ghi VFP/NEON**; yêu cầu có FPU phần cứng tương thích.
- **softfp**: (ở giữa) nội bộ vẫn dùng FPU nếu có, **nhưng ABI tương thích soft** (tham số float vẫn đi qua thanh ghi thường). Dùng để chạy được trên userland soft mà vẫn hưởng lợi FPU bên trong hàm.

# 2) armel vs armhf (tên kiến trúc của Debian/Ubuntu)

- **armel**

  - Mục tiêu: **ARMv4T trở lên**, **không yêu cầu FPU**.
  - **ABI: soft-float** (EABI).
  - Chạy được trên CPU rất cũ (ARM9/ARM11, router đời xưa). Tính toán số thực chậm hơn.
  - Trình nạp động thường là: `/lib/ld-linux.so.3`
  - Toolchain triplet: `arm-linux-gnueabi`

- **armhf**

  - Mục tiêu: **ARMv7 trở lên** (ví dụ Cortex-A7/A8/A9/A15, A53 ở chế độ 32-bit), **bắt buộc có VFPv3-D16** (thường kèm NEON).
  - **ABI: hard-float**.
  - Nhanh hơn rõ rệt với code dùng float/dsp.
  - Trình nạp động: `/lib/ld-linux-armhf.so.3`
  - Toolchain triplet: `arm-linux-gnueabihf`

> Lưu ý: **armhf không “≤” armel**. Chúng là **hai ABI khác nhau và không tương thích nhị phân** (không trộn lẫn thư viện/nhị phân được).

# 3) Tương thích & lựa chọn

- Kernel Linux 32-bit ARM có thể chạy **cả armel lẫn armhf** userland, nhưng **userland phải đồng nhất** (không mix).
- Phần cứng **≥ ARMv7 + VFPv3** ⇒ chọn **armhf**.
- Phần cứng **cũ hơn / không có FPU** ⇒ **armel** (hoặc build “softfp” nếu cần thỏa hiệp).
- Trường hợp đặc biệt như **Raspberry Pi 1/Zero (ARMv6, VFPv2)**: Debian armhf **không chạy** (vì yêu cầu v7); dùng distro build riêng cho v6 (hoặc armel). (Các đời Pi mới v7/v8 chạy armhf/arm64 tốt.)

# 4) Cách kiểm tra trên máy đích

- Kiểm tra đặc tính CPU/FPU:

  ```sh
  cat /proc/cpuinfo
  grep -i 'Features' /proc/cpuinfo   # tìm vfp, vfpv3, neon
  ```

- Kiểm tra nhị phân/ABI:

  ```sh
  file /bin/ls
  readelf -A /bin/ls | grep -i 'VFP\|Tag_ABI_VFP_args'
  # 'Tag_ABI_VFP_args' xuất hiện → hard-float
  ```

- Kiểm tra linker:

  ```sh
  ldd --version 2>/dev/null | head -1
  ls -l /lib/ld-linux*.so.*   # thấy ld-linux-armhf.so.3 (hard) hay ld-linux.so.3 (soft)
  ```

# 5) Biên dịch chéo (GCC/Clang)

- armel (soft):
  `-mfloat-abi=soft`
- armhf (hard):
  `-mfloat-abi=hard -mfpu=vfpv3-d16` (hoặc `-mfpu=neon-vfpv4` tùy CPU)
- softfp:
  `-mfloat-abi=softfp -mfpu=vfpv3-d16`

# 6) Tóm lại

- **armel = soft-float, rất tương thích, chậm với float, chạy được trên ARM rất cũ**.
- **armhf = hard-float, yêu cầu ARMv7+VFPv3, nhanh hơn, không tương thích nhị phân với armel**.
  Chọn theo **phần cứng mục tiêu** và hệ sinh thái thư viện bạn cần dùng.
