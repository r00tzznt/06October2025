# Compile and Debug with ARM

# Table of Contents

- [1. Executing ARM64 Binaries (C to Binary)](#1-executing-arm64-binaries-c-to-binary)
- [2. Executing ARM32 Binaries (C to Binary)](#2-executing-arm32-binaries-c-to-binary)
- [3. Executing ARM Binaries (Assembly to Binary)](#3-executing-arm-binaries-assembly-to-binary)
  - [3.1 ARM-64bit](#31-arm-64bit)
  - [3.2 ARM-32bit](#32-arm-32bit)
- [4. Disassemble ARM Binaries on x86_64](#4-disassemble-arm-binaries-on-x86_64)
- [5. Debugging ARM Binaries on x86_64](#5-debugging-arm-binaries-on-x86_64)
  - [5.1 Debug ARM 32bit](#51-debug-arm-32bit)
  - [5.2 Debug ARM 64bit](#52-debug-arm-64bit)
  - [5.3 Debug ARM with Pwntools](#53-debug-arm-with-pwntools)
- [6. Resource References](#6-Resource-References)

## 1. EXECUTING ARM64 BINARIES (C TO BINARY)

- Trình biên dịch GCC bạn có trên hệ thống của mình sẽ biên dịch mã của bạn cho kiến trúc của hệ thống mà nó chạy, trong trường hợp này là x86_64. Để biên dịch code cho kiến trúc Arm, chúng ta cần sử dụng trình biên dịch chéo.

- Hãy bắt đầu với Arm64 và cài đặt các gói sau:

```bash
sudo apt-get update -y && sudo apt-get dist-upgrade &&
sudo apt install qemu-user qemu-user-static gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu binutils-aarch64-linux-gnu-dbg build-essential
```

- Sau khi cài đặt, hãy tạo một tệp chứa chương trình C đơn giản để thử nghiệm, ví dụ:

```c
#include <stdio.h>

int main(void) {
    return printf("Hello, I'm executing ARM64 instructions!\n");
}
```

- Biên dịch mã dưới dạng thực thi tĩnh, chúng ta có thể sử dụng `aarch64-linux-gnu-gcc` với `-static` flag.

```bash
aarch64-linux-gnu-gcc -static -o hello64s hello64.c
```

- Nhưng điều gì sẽ xảy ra nếu chúng ta chạy tệp thực thi Arm này trên một kiến trúc khác? Thực thi nó trên kiến trúc x86_64 thường sẽ dẫn đến lỗi cho chúng ta biết rằng tệp nhị phân không thể được thực thi do lỗi ở định dạng thực thi.

```bash
./hello64s
bash: ./hello64: cannot execute binary file: Exec format error
```

- Nhị phân aarch64 được liên kết tĩnh của chúng tôi đang chạy trên máy chủ x86_64 của chúng tôi nhờ `qemu-user-static` và biên dịch `-static`.

```bash
./hello64s
Hello, I'm executing ARM64 instructions!
```

- Đối với biên dịch liên kết thư viện động, chạy nó chúng ta cần sử dụng `qemu-aarch64` và cung cấp các thư viện aarch64 thông qua cờ `-L`.

```bash
aarch64-linux-gnu-gcc -o hello64d hello64.c

qemu-aarch64 -L /usr/aarch64-linux-gnu ./hello64d
Hello, I'm executing ARM64 instructions!
```

## 2. EXECUTING ARM32 BINARIES (C TO BINARY)

- Quy trình tương tự áp dụng cho các tệp nhị phân Arm 32 bit, nhưng chúng ta cần cài đặt các gói khác nhau (ngoài các gói `qemu-user` đã cài đặt trước đó).

```bash
sudo apt install gcc-arm-linux-gnueabihf binutils-arm-linux-gnueabihf binutils-arm-linux-gnueabihf-dbg
```

- Chúng ta sẽ sử dụng cùng một chương trình C đơn giản như trước đây và gọi nó là hello32.c:

```c
#include <stdio.h>

int main(void) {
    return printf("Hello, I am an ARM32 binary!\n");
}
```

- Bây giờ chúng tôi biên dịch chương trình này dưới dạng tệp thực thi Arm32 được liên kết tĩnh bằng cách sử dụng `arm-linux-gnueabihf-gcc` với cờ `-static` và chạy nó:

```bash
arm-linux-gnueabihf-gcc -static -o hello32s hello32.c

./hello32s
Hello, I am an ARM32 binary!
```

- Bây giờ hãy biên dịch nó dưới dạng tệp thực thi được liên kết động.

```bash
arm-linux-gnueabihf-gcc -o hello32d hello32.c
```

```bash
qemu-arm -L /usr/arm-linux-gnueabihf ./hello32d
Hello, I am an ARM32 binary!
```

## 3. EXECUTING ARM BINARIES (ASSEMBLY TO BINARY)

### 3.1 ARM-64bit

- Giả sử chúng ta muốn biên dịch chương trình ARM asembly `helloworld` sau đây:

```bash
.section .text
.global _start

_start:
/* syscall write(int fd, const void *buf, size_t count) */
    mov x0, #1
    ldr x1, =msg
    ldr x2, =len
    mov w8, #64
    svc #0

/* syscall exit(int status) */
    mov x0, #0
    mov w8, #93
    svc #0

msg:
.ascii "Hello, ARM64!\n"
len = . - msg
```

- Thông thường chúng tôi sẽ lắp ráp và liên kết nó với AS và LD gốc. Nhưng trình biên dịch bản địa chỉ có thể giải thích các hướng dẫn của kiến trúc mà nó được xây dựng để giải thích, ví dụ: x86_64. Cố gắng lắp ráp các lệnh Arm sẽ dẫn đến lỗi:

```bash
 as asm64.s -o asm64.o && ld asm64.o -o asm64

asm64.s: Assembler messages:
asm64.s: Warning: end of file not at end of a line; newline inserted
asm64.s:6: Error: expecting operand after ','; got nothing
asm64.s:7: Error: no such instruction: `ldr x1,=msg'
asm64.s:8: Error: no such instruction: `ldr x2,=len'
asm64.s:9: Error: expecting operand after ','; got nothing
asm64.s:10: Error: no such instruction: `svc '
asm64.s:13: Error: expecting operand after ','; got nothing
asm64.s:14: Error: expecting operand after ','; got nothing
asm64.s:15: Error: no such instruction: `svc '
```

- Đó là lý do tại sao chúng ta cần sử dụng trình biên dịch chéo và trình liên kết đặc biệt cho tập lệnh của chương trình của chúng ta. Trong trường hợp này, A64:

```bash
aarch64-linux-gnu-as asm64.s -o asm64.o && aarch64-linux-gnu-ld asm64.o -o asm64

./asm64
Hello, ARM64!
```

### 3.2 ARM-32bit

- Hãy làm tương tự cho phiên bản A32 của chương trình này:

```bash
.section .text
.global _start

_start:
/* syscall write(int fd, const void *buf, size_t count) */
    mov r0, #1
    ldr r1, =msg
    ldr r2, =len
    mov r7, #4
    svc #0

/* syscall exit(int status) */
    mov r0, #0
    mov r7, #1
    svc #0

msg:
.ascii "Hello, ARM32!\n"
len = . - msg
```

- Lắp ráp và liên kết và...

```bash
arm-linux-gnueabihf-as asm32.s -o asm32.o && arm-linux-gnueabihf-ld -static asm32.o -o asm32

./asm32
Hello, ARM32!
```

## 4. DISASSEMBLE ARM BINARIES ON X86_64

- Cách dễ nhất để xem xét việc tháo gỡ một nhị phân ELF là với một công cụ gọi là objdump. Điều này đặc biệt hữu ích cho các tệp nhị phân nhỏ.

- Nhưng điều gì sẽ xảy ra nếu chúng ta sử dụng objdump gốc từ hệ thống máy chủ của chúng ta để tháo rời một hệ nhị phân Arm?

```bash
objdump -d hello32d

hello32d:     file format elf32-little

objdump: can't disassemble for architecture UNKNOWN!
```

- Tất cả những gì chúng ta cần là xây dựng chéo của nó. Nếu bạn nhập "arm-linux" vào thiết bị đầu cuối của mình và nhấn đúp, bạn sẽ thấy tất cả các tiện ích chúng tôi đã cài đặt với gói `binutils-arm-linux-gnueabihf` và `objdump` được bao gồm!

```bash
arm-linux-gnueabihf-                                         arm-linux-gnueabihf-addr2line      arm-linux-gnueabihf-gcov-dump
arm-linux-gnueabihf-ar             arm-linux-gnueabihf-gcov-dump-11
arm-linux-gnueabihf-as             arm-linux-gnueabihf-gcov-tool
arm-linux-gnueabihf-c++filt        arm-linux-gnueabihf-gcov-tool-11
arm-linux-gnueabihf-cpp            arm-linux-gnueabihf-gprof
arm-linux-gnueabihf-cpp-11         arm-linux-gnueabihf-ld
arm-linux-gnueabihf-dwp            arm-linux-gnueabihf-ld.bfd
arm-linux-gnueabihf-elfedit        arm-linux-gnueabihf-ld.gold
arm-linux-gnueabihf-gcc            arm-linux-gnueabihf-lto-dump-11
arm-linux-gnueabihf-gcc-11         arm-linux-gnueabihf-nm
arm-linux-gnueabihf-gcc-ar         arm-linux-gnueabihf-objcopy
arm-linux-gnueabihf-gcc-ar-11      arm-linux-gnueabihf-objdump
arm-linux-gnueabihf-gcc-nm         arm-linux-gnueabihf-ranlib
arm-linux-gnueabihf-gcc-nm-11      arm-linux-gnueabihf-readelf
arm-linux-gnueabihf-gcc-ranlib     arm-linux-gnueabihf-size
arm-linux-gnueabihf-gcc-ranlib-11  arm-linux-gnueabihf-strings
arm-linux-gnueabihf-gcov           arm-linux-gnueabihf-strip
arm-linux-gnueabihf-gcov-11
```

- Bây giờ tất cả những gì chúng ta phải làm là sử dụng arm-linux-gnueabihf-objdump. Hãy thử điều này với tệp nhị phân asm32:

```bash
arm-linux-gnueabihf-objdump -d asm32

asm32:     file format elf32-littlearm


Disassembly of section .text:

00010054 <_start>:
   10054:       e3a00001        mov     r0, #1
   10058:       e59f1024        ldr     r1, [pc, #36]   ; 10084 <msg+0x10>
   1005c:       e59f2024        ldr     r2, [pc, #36]   ; 10088 <msg+0x14>
   10060:       e3a07004        mov     r7, #4
   10064:       ef000000        svc     0x00000000
   10068:       e3a00000        mov     r0, #0
   1006c:       e3a07001        mov     r7, #1
   10070:       ef000000        svc     0x00000000

00010074 <msg>:
   10074:       6c6c6548        .word   0x6c6c6548
   10078:       41202c6f        .word   0x41202c6f
   1007c:       32334d52        .word   0x32334d52
   10080:       00000a21        .word   0x00000a21
   10084:       00010074        .word   0x00010074
   10088:       0000000e        .word   0x0000000e
```

## 5. DEBUGGING ARM BINARIES ON X86_64

### 5.1 DEBUG ARM 32bit

- Chúng tôi cũng có thể gỡ lỗi các tệp nhị phân này trên hệ thống máy chủ của mình, nhưng không phải với cài đặt GDB gốc. Đối với các nhị phân Arm của chúng tôi, chúng tôi sẽ sử dụng `gdb-multiarch`.

```bash
sudo apt install gdb-multiarch qemu-user
```

- Chúng tôi cũng có thể biên dịch mã C của mình với cờ `-ggdb3` tạo ra thông tin gỡ lỗi bổ sung cho GDB. Hãy biên dịch một tệp thực thi được liên kết tĩnh cho ví dụ này:

```bash
arm-linux-gnueabihf-gcc -ggdb3 -o hello32-static hello32.c -static
```

- Một trong những cách chúng ta có thể gỡ lỗi nhị phân này là sử dụng trình giả lập `qemu-user` và đã yêu cầu GDB kết nối với nó thông qua cổng TCP. Để thực hiện việc này, chúng tôi chạy `qemu-arm` với cờ `-g` và số cổng mà nó sẽ đợi kết nối GDB. Cờ `-L` đặt tiền tố trình thông dịch ELF thành đường dẫn mà chúng tôi cung cấp.

```bash
qemu-arm -L /usr/arm-linux-gnueabihf -g 1234 ./hello32-static
```

- Mở một cửa sổ terminal khác và sử dụng lệnh sau:
  - Cờ `–nh` hướng dẫn nó không đọc tệp `.gdbinit` (nó có thể bị lỗi nếu bạn đã cài đặt trình bao bọc GDB).
  - Các tùy chọn `-ex` là các lệnh chúng ta muốn gdb-multiarch đặt khi bắt đầu phiên. Cái đầu tiên đặt kiến trúc đích thành arm (sử dụng arm64 cho nhị phân 64 bit), sau đó chúng tôi cung cấp chính nhị phân, cho nó biết nơi tìm nhị phân đang chạy trong mô phỏng qemu-arm của chúng tôi. Hai lệnh cuối cùng được sử dụng để tách và hiển thị các cửa sổ nguồn, tháo gỡ, lệnh và đăng ký.

```bash
gdb-multiarch -q --nh -ex 'set architecture arm' -ex 'file hello32-static' -ex 'target remote localhost:1234' -ex 'layout split' -ex 'layout regs'
```

### 5.2 DEBUG ARM 64bit

- Đối với AArch64, bạn cần chạy nó với `qemu-aarch64` và đặt kiến trúc đích trong `gdb-multiarch` thành `arm64`:

```bash
qemu-aarch64 -L /usr/aarch64-linux-gnu/ -g 1234 ./hello64
```

- Mở một cửa sổ terminal khác và sử dụng lệnh sau:

```bash
gdb-multiarch -q --nh -ex 'set architecture arm64' -ex 'file hello64' -ex 'target remote localhost:1234' -ex 'layout split' -ex 'layout regs'
```

> Đối với các tệp nhị phân được liên kết động, gdb-multiarch sẽ phàn nàn về việc thiếu thư viện. Nếu điều này xảy ra, hãy chạy lệnh này trong gdb-multiarch và cung cấp đường dẫn đến các thư viện:

```bash
For AArch64:
(gdb) set solib-search-path /usr/aarch64-linux-gnu/lib/
```

```bash
For AArch32:
(gdb) set solib-search-path /usr/arm-linux-gnueabihf/lib/
```

### 5.3 DEBUG ARM with PWNTOOLS

- Debug GDB script pwntolls with QEMU
  - https://docs.pwntools.com/en/stable/qemu.html

```bash
sudo mkdir /etc/qemu-binfmt
sudo ln -s /usr/aarch64-linux-gnu /etc/qemu-binfmt/aarch64
sudo ln -s /usr/arm-linux-gnueabihf /etc/qemu-binfmt/arm
```

- Logic của việc debug bằng qemu là dùng option `-g <port>` rồi mở gdb để remote debug đến.

```bash
[+] Starting local process '/usr/bin/qemu-arm-static' argv=[b'qemu-arm-static', b'-g', b'62354', b'-L', b'/etc/qemu-binfmt/arm', b'/home/nigmaz/Documents/ARM/arm-exploit/arm-exploit'] : pid 6173
[DEBUG] Wrote gdb script to '/tmp/pwn5rw5_qmz.gdb'
    set sysroot /etc/qemu-binfmt/arm
    set endian little
    set architecture arm
    file "/home/nigmaz/Documents/ARM/arm-exploit/arm-exploit"
    target remote 127.0.0.1:62354

                b *main

[*] running in new terminal: ['/usr/bin/gdb-multiarch', '-q', '-x', '/tmp/pwn5rw5_qmz.gdb']

```

- Chạy script process

```python
from pwn import *

# Đường dẫn đến các tệp thư viện và linker
ld_path = "./ld-linux-aarch64.so.1"
libc_path = "./libc.so.6"
pacsh_path = "./pacsh"

# Tạo đối tượng process với Pwntools
p = process([ld_path, "--library-path", ".", pacsh_path])

# Tương tác với process
p.interactive()
```

- gdb.attach()

```bash
sudo apt install terminator
```

```python
from pwn import *

elf = ELF("./pacsh")
libc = ELF("./libc.so.6")
ld = ELF("./ld-linux-aarch64.so.1")
ROP_LOAD = ROP("./libc.so.6")
context.update(binary=elf, log_level="DEBUG")
# Đường dẫn đến các tệp thư viện và linker
ld_path = "./ld-linux-aarch64.so.1"
libc_path = "./libc.so.6"
pacsh_path = "./pacsh"

# Tạo đối tượng process với Pwntools
p = process([ld_path, "--library-path", ".", pacsh_path])
gdb.attach(p, '''
    b *main
    b *ls
    b *read64
    b *write64
    b *main+152
    ''')

# Tương tác với process
p.interactive()
```

- gdb.debug()

```python
#!/usr/bin/env python3
import sys
from pwn import *

elf = ELF("./pacsh")
libc = ELF(elf.libc.path)
ld = ELF("./ld-linux-aarch64.so.1")
ROP_LOAD = ROP("./libc.so.6")
context.update(binary=elf, log_level="DEBUG")

if args.LOCAL:
    # p = process(["qemu-aarch64", "./pacsh"])

    ld_path = "./ld-linux-aarch64.so.1"
    libc_path = "./libc.so.6"
    pacsh_path = "./pacsh"
    p = process([ld_path, "--library-path", ".", pacsh_path])
    # qemu-aarch64 -L . -E LD_LIBRARY_PATH=. ./ld-linux-aarch64.so.1 ./pacsh

    # p = process(["qemu-aarch64", "-L", "/usr/aarch64-linux-gnu", chall_path])
    # qemu-aarch64 -L . -E LD_LIBRARY_PATH=. ./ld-linux-aarch64.so.1 ./pacsh

    # p = process('./run.sh', env={'LD_PRELOAD' :'./libc.so.6'})
elif args.GDB:
    p = gdb.debug([elf.path], '''
            b *main
            b *ls
            b *read64
            b *write64
            b *main+152
        ''')
    # x/10gx &BUILTINS
else:
    libc = ELF("./libc.so.6")
    p = remote("2024.ductf.dev", "30027")

# if args.GDB:
#     raw_input("GDB")  # Use input("GDB") if using Python 3

p.interactive()
```

## 6. Resource References:

- https://azeria-labs.com/arm-on-x86-qemu-user/

- https://azeria-labs.com/debugging-with-gdb-introduction/

- Hiện tại Ubuntu 24.04 build hết theo follow trên thì bị lỗi này

```bash
b"qemu-aarch64-static: Could not open '/lib/ld-linux-aarch64.so.1': No such file or directory\n"
```

- http://ports.ubuntu.com/dists/noble-updates/universe/

- https://www.reddit.com/r/Ubuntu/comments/1cmcpbs/ubuntu_noble_2404_does_not_support/

- https://ughe.github.io/2018/07/19/qemu-aarch64
