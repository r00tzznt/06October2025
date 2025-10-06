# MIPS

- mips pwntools exploit:

```bash
sudo apt-get install gcc-mips-linux-gnu -y
```

```python
#!/usr/bin/env python3
import sys
from pwn import *

elf = ELF("./backup-power")
context.update(binary=elf, log_level="DEBUG")


if args.LOCAL:
    p = process(["qemu-mips-static", elf.path])
    # qemu-mips-static ./backup-power

elif args.GDB:
    p = gdb.debug([elf.path], '''
            b *main
        ''')
else:
    p = remote("backup-power.chal.uiuc.tf", 1337, ssl=True)


p.interactive()
```

**Giải thích các thanh ghi trong MIPS**

MIPS (Microprocessor without Interlocked Pipeline Stages) là một kiến trúc vi xử lý dạng RISC (Reduced Instruction Set Computer). MIPS có 32 thanh ghi tổng quát (general-purpose registers), mỗi thanh ghi có kích thước 32 bit, và một số thanh ghi đặc biệt khác. Dưới đây là mô tả về các thanh ghi chính trong MIPS:

1. **Thanh ghi tổng quát (General-purpose registers):**

   - `$zero` (r0): Luôn luôn có giá trị 0.
   - `$at` (r1): Sử dụng cho assembler temporary.
   - `$v0 - $v1` (r2 - r3): Sử dụng để trả về giá trị từ hàm (return values).
   - `$a0 - $a3` (r4 - r7): Sử dụng để truyền tham số cho hàm (arguments).
   - `$t0 - $t7` (r8 - r15): Sử dụng cho mục đích tạm thời (temporaries).
   - `$s0 - $s7` (r16 - r23): Sử dụng cho mục đích lưu trữ (saved registers).
   - `$t8 - $t9` (r24 - r25): Sử dụng cho mục đích tạm thời (temporaries).
   - `$k0 - $k1` (r26 - r27): Sử dụng cho kernel (reserved for OS kernel).
   - `$gp` (r28): Global pointer.
   - `$sp` (r29): Stack pointer.
   - `$fp` (r30): Frame pointer hoặc $s8 (trong một số tài liệu).
   - `$ra` (r31): Return address.

2. **Thanh ghi đặc biệt:**
   - `HI` và `LO`: Lưu kết quả của phép nhân và chia.
   - `PC` (Program Counter): Chứa địa chỉ của lệnh hiện tại.

**Bảng so sánh thanh ghi MIPS với Intel x86**

| **MIPS** | **Miêu tả**                 | **Intel x86** | **Miêu tả**                       |
| -------- | --------------------------- | ------------- | --------------------------------- |
| `$zero`  | Luôn luôn có giá trị 0      | Không có      | -                                 |
| `$at`    | Assembler temporary         | Không có      | -                                 |
| `$v0`    | Return value                | `EAX`         | Return value                      |
| `$v1`    | Return value                | `EDX`         | Return value (cho 64-bit giá trị) |
| `$a0`    | Argument 0                  | `ECX`         | Argument 1                        |
| `$a1`    | Argument 1                  | `EDX`         | Argument 2                        |
| `$a2`    | Argument 2                  | `EBX`         | Argument 3                        |
| `$a3`    | Argument 3                  | `ESI`         | Argument 4                        |
| `$t0`    | Temporary                   | `EBX`         | Temporary                         |
| `$t1`    | Temporary                   | `ECX`         | Temporary                         |
| `$t2`    | Temporary                   | `EDX`         | Temporary                         |
| `$t3`    | Temporary                   | `ESI`         | Temporary                         |
| `$t4`    | Temporary                   | `EDI`         | Temporary                         |
| `$t5`    | Temporary                   | `EBP`         | Temporary                         |
| `$t6`    | Temporary                   | `EAX`         | Temporary                         |
| `$t7`    | Temporary                   | `ESP`         | Stack Pointer                     |
| `$s0`    | Saved register              | `EBP`         | Saved register                    |
| `$s1`    | Saved register              | `EBX`         | Saved register                    |
| `$s2`    | Saved register              | `ESI`         | Saved register                    |
| `$s3`    | Saved register              | `EDI`         | Saved register                    |
| `$s4`    | Saved register              | `EBP`         | Saved register                    |
| `$s5`    | Saved register              | `EBX`         | Saved register                    |
| `$s6`    | Saved register              | `ESI`         | Saved register                    |
| `$s7`    | Saved register              | `EDI`         | Saved register                    |
| `$t8`    | Temporary                   | `EAX`         | Temporary                         |
| `$t9`    | Temporary                   | `EDX`         | Temporary                         |
| `$k0`    | Reserved for OS kernel      | Không có      | -                                 |
| `$k1`    | Reserved for OS kernel      | Không có      | -                                 |
| `$gp`    | Global pointer              | `GS`          | Global segment register           |
| `$sp`    | Stack pointer               | `ESP`         | Stack pointer                     |
| `$fp`    | Frame pointer               | `EBP`         | Frame pointer                     |
| `$ra`    | Return address              | Không có      | -                                 |
| `HI`     | Multiply/divide high result | Không có      | -                                 |
| `LO`     | Multiply/divide low result  | Không có      | -                                 |
| `PC`     | Program Counter             | `EIP`         | Instruction pointer               |

Bảng trên chỉ ra sự khác biệt và tương đồng giữa các thanh ghi của MIPS và Intel x86. Mỗi kiến trúc có các quy ước riêng cho việc sử dụng thanh ghi, với MIPS có một cách đặt tên nhất quán và rõ ràng hơn so với Intel x86.
