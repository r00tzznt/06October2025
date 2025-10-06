# ARM assembly Language Programming

> ARM assembly Language Programming start...

- https://azeria-labs.com/writing-arm-assembly-part-1/

- https://chromium.googlesource.com/chromiumos/docs/+/master/constants/syscalls.md

- `Calling convention ARM`: https://medium.com/@mbugraavci38/mastering-arm-assembly-calling-functions-in-the-arm-architecture-a4e50e465af

- `Compiler online`: https://cpulator.01xz.net/?sys=arm-de1soc

## Table of Contents

- [1. Overview ARM Processor](#1-overview-arm-processor)
  - [1.1 ARM Register 32 bit](#11-arm-register-32-bit)
  - [1.2 ARM Register 64 bit](#12-arm-register-64-bit)
- [2. ARM Instructions](#2-arm-instructions)
  - [2.1 Overview ARM Instruction](#21-overview-arm-instruction)
  - [2.2 Compare ARM with Intel](#22-compare-arm-with-intel)
  - [2.3 ARM Instruction Examples](#23-arm-instruction-examples)
  - [2.4 Gadget Exploits in ARM](#24-gadget-exploits-in-arm)
- [3. Resource References](#3-Resource-References)

## 1. Overview ARM Processor

- Có nhiều sự khác biệt giữa Intel và ARM, nhưng sự khác biệt chính là tập lệnh. Intel là bộ xử lý CISC (Complex Instruction Set Computing) có tập lệnh lớn hơn và giàu tính năng hơn và cho phép nhiều lệnh phức tạp truy cập bộ nhớ. Do đó, nó có nhiều hoạt động hơn, chế độ địa chỉ, nhưng ít thanh ghi hơn ARM. Bộ xử lý CISC chủ yếu được sử dụng trong PC, Máy trạm và máy chủ thông thường.

- ARM là bộ xử lý RISC (Reduced instruction set Computing) và do đó có tập lệnh đơn giản hóa (100 lệnh hoặc ít hơn) và các thanh ghi mục đích chung hơn CISC. Không giống như Intel, ARM sử dụng các hướng dẫn chỉ hoạt động trên các thanh ghi và sử dụng mô hình bộ nhớ Load/Store để truy cập bộ nhớ, có nghĩa là chỉ các lệnh Load/Store mới có thể truy cập bộ nhớ. Điều này có nghĩa là việc tăng giá trị 32 bit tại một địa chỉ bộ nhớ cụ thể trên ARM sẽ yêu cầu ba loại lệnh (tải, tăng và lưu trữ) để trước tiên tải giá trị tại một địa chỉ cụ thể vào một thanh ghi, tăng nó trong thanh ghi và lưu trữ nó trở lại bộ nhớ từ thanh ghi.

- Nhiều điểm khác biệt giữa ARM và x86 là:

  - Trong ARM, hầu hết các lệnh có thể được sử dụng để thực hiện có điều kiện.
  - Dòng bộ xử lý Intel x86 và x86-64 sử dụng định dạng little-endian.
  - Kiến trúc ARM little-endian kết thúc trước phiên bản 3. Kể từ đó, bộ xử lý ARM đã trở thành BI-endian (Big Endian và Little Endian) và có cài đặt cho phép chuyển đổi endianness.

### 1.1 ARM Register 32 bit

- Thanh ghi kiến trúc ARM (ARM Architecture Registers) là các thanh ghi đặc biệt trong vi điều khiển ARM (vi điều khiển loại RISC - Reduced Instruction Set Computer). Dưới đây là một số thanh ghi kiến trúc quan trọng trong vi điều khiển ARM (32-bit):

1. **R0-R15**: Là 16 thanh ghi tổng hợp (general-purpose registers). Chúng được sử dụng để lưu trữ dữ liệu và kết quả của các phép toán.

2. **R0 - R7**: Còn được gọi là thanh ghi tham số (parameter registers) hoặc thanh ghi hàm (scratch registers). Chúng được sử dụng để truyền tham số và lưu giá trị tạm thời trong quá trình thực thi các hàm.

3. **R8 - R11**: Còn được gọi là thanh ghi cốt lõi (core registers). Chúng thường được sử dụng để lưu các giá trị quan trọng, và có thể bị ghi đè bởi các hàm con.

4. **R12**: Còn được gọi là thanh ghi IP (Intra-Procedure-call scratch register). Đôi khi được sử dụng để lưu trữ các giá trị tạm thời.

5. **R13**: Còn được gọi là thanh ghi SP (Stack Pointer). Nó lưu trữ địa chỉ của đỉnh của ngăn xếp (stack).

6. **R14**: Còn được gọi là thanh ghi LR (Link Register). Nó lưu trữ địa chỉ của lệnh sau khi gọi hàm.

7. **R15**: Còn được gọi là thanh ghi PC (Program Counter). Nó lưu trữ địa chỉ của lệnh đang được thực thi.

8. **CPSR**: Là thanh ghi trạng thái (Current Program Status Register) chứa các cờ và trạng thái của trình tự hiện tại.

9. **SPSR**: Là thanh ghi trạng thái không gian người dùng (Saved Program Status Register), được sử dụng để lưu trữ trạng thái trước khi có ngắt.

10. **FPSID**: Là thanh ghi ID số động của FPU (Floating Point Unit), được sử dụng cho tính toán số thực.

11. **FPSCR**: Là thanh ghi trạng thái của FPU.

12. **FPEXC**: Là thanh ghi trạng thái ngoại lệ của FPU.

- Bảng biểu diễn nội dung thanh ghi ARM-32bit:

| Register | Alias | Purpose                         |
| -------- | ----- | ------------------------------- |
| R0       | –     | General purpose                 |
| R1       | –     | General purpose                 |
| R2       | –     | General purpose                 |
| R3       | –     | General purpose                 |
| R4       | –     | General purpose                 |
| R5       | –     | General purpose                 |
| R6       | –     | General purpose                 |
| R7       | –     | Holds Syscall Number            |
| R8       | –     | General purpose                 |
| R9       | –     | General purpose                 |
| R10      | –     | General purpose                 |
| R11      | FP    | Frame Pointer                   |
| R12      | IP    | Intra Procedural Call           |
| R13      | SP    | Stack Pointer                   |
| R14      | LR    | Link Register                   |
| R15      | PC    | Program Counter                 |
| CPSR     | –     | Current Program Status Register |

- Dưới đây là bảng biểu diễn các thanh ghi ARM 32-bit khi so sánh với Intel 32-bit:

| ARM      | Description                                 | x86                     |
| -------- | ------------------------------------------- | ----------------------- |
| R0       | General Purpose                             | EAX                     |
| R1-R5    | General Purpose                             | EBX, ECX, EDX, ESI, EDI |
| R6-R10   | General Purpose                             | –                       |
| R11 (FP) | Frame Pointer                               | EBP                     |
| R12      | Intra Procedural Call                       | –                       |
| R13 (SP) | Stack Pointer                               | ESP                     |
| R14 (LR) | Link Register                               | –                       |
| R15 (PC) | <- Program Counter / Instruction Pointer -> | EIP                     |
| CPSR     | Current Program State Register/Flags        | EFLAGS                  |

### 1.2 ARM Register 64 bit

> https://valsamaras.medium.com/arm-64-assembly-series-basic-definitions-and-registers-ec8cc1334e40

- Dưới đây là bảng biểu diễn các thanh ghi ARM 64-bit khi so sánh với Intel 64-bit:

| ARM 64-bit | Description                         | Intel 64-bit            |
| ---------- | ----------------------------------- | ----------------------- |
| X0         | General Purpose                     | RAX                     |
| X1-X5      | General Purpose                     | RBX, RCX, RDX, RSI, RDI |
| X6-X15     | General Purpose                     | –                       |
| X16-X17    | Intra Procedural Call               | –                       |
| X18        | Platform Register                   | –                       |
| X19-X28    | General Purpose                     | –                       |
| X29 (FP)   | Frame Pointer                       | RBP                     |
| X30 (LR)   | Link Register                       | –                       |
| X31 (SP)   | Stack Pointer                       | RSP                     |
| PC         | Program Counter                     | RIP                     |
| SPSR       | Saved Program Status Register/Flags | RFLAGS                  |

Trong kiến trúc ARM64 (AArch64), thanh ghi X0 và W0 thực chất là hai dạng biểu diễn khác nhau của cùng một thanh ghi vật lý:

- X0: 64-bit (đầy đủ)

- W0: 32-bit (phần thấp của X0)

Quan hệ giữa X0 và W0 :

- Khi bạn ghi vào W0, phần thấp 32-bit của X0 sẽ được cập nhật, và phần cao 32-bit còn lại của X0 sẽ bị xóa (set về 0).

- Khi bạn ghi vào X0, toàn bộ 64-bit sẽ được thay đổi, và do đó W0 cũng phản ánh phần thấp của giá trị mới trong X0.

## 2. ARM INSTRUCTION

### 2.1 Overview ARM instruction

- Bảng biểu diễn các lệnh ARM cùng với mô tả của chúng:

| Instruction | Description            | Instruction | Description                   |
| ----------- | ---------------------- | ----------- | ----------------------------- |
| MOV         | Move data              | EOR         | Bitwise XOR                   |
| MVN         | Move and negate        | LDR         | Load                          |
| ADD         | Addition               | STR         | Store                         |
| SUB         | Subtraction            | LDM         | Load Multiple                 |
| MUL         | Multiplication         | STM         | Store Multiple                |
| LSL         | Logical Shift Left     | PUSH        | Push on Stack                 |
| LSR         | Logical Shift Right    | POP         | Pop off Stack                 |
| ASR         | Arithmetic Shift Right | B           | Branch                        |
| ROR         | Rotate Right           | BL          | Branch with Link              |
| CMP         | Compare                | BX          | Branch and eXchange           |
| AND         | Bitwise AND            | BLX         | Branch with Link and eXchange |
| ORR         | Bitwise OR             | SWI/SVC     | System Call                   |

- Nói chung, LDR được sử dụng để tải một cái gì đó từ bộ nhớ vào một thanh ghi và STR được sử dụng để lưu trữ một cái gì đó từ một thanh ghi đến một địa chỉ bộ nhớ.

### 2.2 Compare ARM with Intel

- Bảng so sánh cú pháp ARM với Intel:

| ARM Instruction | Description                            | Intel Instruction | Description                  |
| --------------- | -------------------------------------- | ----------------- | ---------------------------- |
| MOV             | Move data                              | MOV               | Move data                    |
| MVN             | Move and negate                        | NOT               | Bitwise NOT                  |
| ADD             | Addition                               | ADD               | Addition                     |
| SUB             | Subtraction                            | SUB               | Subtraction                  |
| MUL             | Multiplication                         | IMUL              | Signed multiplication        |
| LSL             | Logical Shift Left                     | SHL               | Logical Shift Left           |
| LSR             | Logical Shift Right                    | SHR               | Logical Shift Right          |
| ASR             | Arithmetic Shift Right                 | SAR               | Arithmetic Shift Right       |
| ROR             | Rotate Right                           | ROR               | Rotate Right                 |
| CMP             | Compare                                | CMP               | Compare                      |
| AND             | Bitwise AND                            | AND               | Bitwise AND                  |
| ORR             | Bitwise OR                             | OR                | Bitwise OR                   |
| EOR             | Bitwise XOR                            | XOR               | Bitwise XOR                  |
| LDR             | Load                                   | MOV               | Move data (load equivalent)  |
| STR             | Store                                  | MOV               | Move data (store equivalent) |
| LDM             | Load Multiple                          | –                 | –                            |
| STM             | Store Multiple                         | –                 | –                            |
| PUSH            | Push on Stack                          | PUSH              | Push on Stack                |
| POP             | Pop off Stack                          | POP               | Pop off Stack                |
| B               | Branch                                 | JMP               | Unconditional Jump           |
| BL              | Branch with Link                       | CALL              | Call Procedure               |
| BX              | Branch and eXchange                    | JMP               | Unconditional Jump           |
| BLX             | Branch with Link and eXchange          | CALL              | Call Procedure               |
| SWI/SVC         | System Call                            | INT               | Software Interrupt           |
| –               | –                                      | LEA               | Load Effective Address       |
| BX LR           | Branch to the address in Link Register | RET               | Return from procedure        |

- `Lệnh LEA (Load Effective Address) trong Intel x86 không có lệnh tương đương trực tiếp trong ARM`: Tuy nhiên, các lệnh ARM có thể được sử dụng để đạt được hiệu quả tương tự bằng cách kết hợp các lệnh khác nhau.

  - Giải thích thêm về LEA:

    - **LEA (Load Effective Address)**: Được sử dụng để tính toán địa chỉ bộ nhớ mà không thực sự truy cập bộ nhớ. Nó tải địa chỉ hiệu quả vào một thanh ghi. Ví dụ, `LEA EAX, [EBX + ECX * 4]` sẽ tính toán địa chỉ `EBX + ECX * 4` và lưu trữ vào thanh ghi `EAX`.

  - Tương đương trong ARM:
    - ARM không có lệnh tương đương trực tiếp với LEA, nhưng có thể sử dụng các lệnh khác để tính toán địa chỉ hiệu quả, ví dụ:
    - **ADD**: `ADD R0, R1, R2, LSL #2` để tính toán `R1 + R2 * 4` và lưu kết quả vào `R0`.
    - **ADR/ADRP**: `ADR` và `ADRP` có thể được sử dụng để tải địa chỉ tương đối vào thanh ghi.
    - Hoặc là tải địa chỉ tương đối bằng **LDR**.

- Giải thích thêm về `RET`: **RET (Return)**: Lệnh này pop địa chỉ trả về từ stack và nhảy đến địa chỉ đó, kết thúc việc thực hiện hàm và trả về cho hàm gọi nó.

  - Tương đương trong ARM: **BX LR (Branch and eXchange to Link Register)**: Lệnh này chuyển điều khiển đến địa chỉ được lưu trữ trong thanh ghi Link Register (LR). Trong ARM, khi một hàm được gọi bằng `BL` (Branch with Link), địa chỉ trả về sẽ được lưu trữ trong `LR`. Để trở về hàm gọi, `BX LR` được sử dụng.

    ```bash
    BX LR
    ```

  - Trong ARM, nếu sử dụng ARMv8 (AArch64), lệnh tương đương sẽ là `RET`:

    ```bash
    RET
    ```

> https://azeria-labs.com/writing-arm-assembly-part-1/

### 2.3 ARM Instruction Examples

- 1. **MOV**: Đây là lệnh để di chuyển giá trị từ một địa chỉ đến một thanh ghi hoặc giữa các thanh ghi.

```bash
MOV Rd, #imm

MOV r0, #10     ; Đặt giá trị 10 vào thanh ghi r0
MOV r1, r2      ; Gán giá trị của r2 vào r1
```

- 2. **ADD, SUB**: Đây là các lệnh để thực hiện phép cộng và trừ. Ví dụ:

```bash
ADD Rd, Rn, Rm

ADD r3, r3, #5  ; r3 = r3 + 5
SUB r4, r4, r5  ; r4 = r4 - r5
```

- 3. **MUL, DIV**: Đây là các lệnh thực hiện phép nhân và chia. Ví dụ:

```bash
MUL Rd, Rn, Rm
```

Multiplies the values in registers `Rn` and `Rm`, and stores the result in register `Rd`.

```bash
SDIV Rd, Rn, Rm
```

SDIV (Signed Divide): Performs a signed division of the value in register Rn by the value in register Rm and stores the result in register Rd.

```bash
UDIV Rd, Rn, Rm
```

UDIV (Unsigned Divide): Performs an unsigned division of the value in register Rn by the value in register Rm and stores the result in register Rd.

**AND (Logical AND)**

```bash
AND Rd, Rn, Rm
```

Performs a bitwise AND operation on the values in registers `Rn` and `Rm`, and stores the result in register `Rd`.

**ORR (Logical OR)**

```bash
ORR Rd, Rn, Rm
```

Performs a bitwise OR operation on the values in registers `Rn` and `Rm`, and stores the result in register `Rd`.

**EOR (Exclusive OR)**

```bash
EOR Rd, Rn, Rm
```

Performs a bitwise XOR operation on the values in registers `Rn` and `Rm`, and stores the result in register `Rd`.

- 4. **CMP**: Lệnh này so sánh hai giá trị và cập nhật các cờ tương ứng. Ví dụ:

```bash
CMP r0, r1      ; So sánh giá trị trong r0 và r1
```

- 5. **B**: Lệnh này là lệnh nhảy không điều kiện. Nó sẽ nhảy đến một địa chỉ khác trong chương trình. Ví dụ:

```bash
B label_name    ; Nhảy đến địa chỉ được định nghĩa bởi label_name
```

**BL (Branch with Link)**

```bash
BL label
```

Branches to the instruction at the specified label and saves the return address in the link register (LR).

**BX (Branch and Exchange)**

```bash
BX Rm
```

Branches to the address contained in register `Rm` and possibly changes the instruction set (Thumb/ARM).

- 6. **BEQ, BNE, BGT, BLT, ...**: Các lệnh nhảy có điều kiện, sẽ nhảy đến một địa chỉ khác dựa trên trạng thái của các cờ (VD: Bằng nhau, Không bằng nhau, Lớn hơn, Nhỏ hơn, ...).

- 7. **LDR, STR**: Lệnh này để đọc và ghi dữ liệu từ và vào bộ nhớ. Ví dụ:

```bash
LDR Rd, [Rn, #offset]
LDR r0, [r1]    ; Load giá trị từ địa chỉ chứa bởi r1 vào r0

STR Rd, [Rn, #offset]
STR r2, [r3]    ; Ghi giá trị trong r2 vào địa chỉ chứa bởi r3
```

- 8. **LDM, STM**: Lệnh này để load và store nhiều thanh ghi cùng lúc. Ví dụ:

```bash
LDMIA r4!, {r5, r6, r7}   ; Load giá trị từ r4, sau đó tăng r4 và gán vào r5, r6, r7.
```

- 9. **PUSH, POP**: Lệnh này được sử dụng để thêm và xóa dữ liệu từ ngăn xếp. Ví dụ:

```bash
PUSH {r0, r1, r2}   ; Đẩy r0, r1, r2 vào ngăn xếp
POP {r0, r1, r2}    ; Lấy dữ liệu từ ngăn xếp vào r0, r1, r2
```

- 10. **BL, BX**: Lệnh BL dùng để gọi một hàm con (subroutine). Lệnh BX dùng để nhảy về nơi đã gọi hàm con đó.

```bash
BL function_name    ; Gọi hàm con tên function_name
BX lr              ; Nhảy về nơi đã gọi hàm con
```

- 11. **NOP**: Lệnh này không thực hiện bất kỳ thao tác nào. Nó thường được sử dụng để tạo độ trễ hoặc đánh dấu vị trí trong mã.

```bash
NOP   ; No operation
```

- 12. **ADRP**: Lệnh `ADRP` trong hợp ngữ ARM64 (AArch64) dùng để tính địa chỉ cơ bản (base address) của một biến hoặc mảng trong bộ nhớ. Lệnh này thường đi kèm với lệnh `LDR` để lấy giá trị tại địa chỉ đã tính toán.

Cú pháp của lệnh `ADRP` như sau:

```bash
ADRP Xd, label
```

Trong đó:

- `Xd` là thanh ghi đích (destination register) để lưu giá trị địa chỉ tính toán.
- `label` là nhãn (label) của biến hoặc mảng cần tính địa chỉ cơ bản.

Lệnh `ADRP` tính toán địa chỉ cơ bản bằng cách lấy địa chỉ của nhãn `label`, sau đó xóa đi 12 bit cuối của địa chỉ này (vì các địa chỉ thường được căn chỉnh ở dạng byte, và 12 bit cuối đại diện cho các offset trong một trang 4KB). Kết quả là địa chỉ cơ bản của biến hoặc mảng đó sẽ được lưu vào thanh ghi `Xd`.

Sau khi đã tính được địa chỉ cơ bản, bạn có thể sử dụng lệnh `LDR` hoặc các lệnh khác để truy xuất dữ liệu tại địa chỉ đó.

Ví dụ:

```bash
ADRP X0, my_array    ; Tính địa chỉ cơ bản của mảng my_array và lưu vào X0
LDR  X1, [X0, #8]    ; Load giá trị tại địa chỉ X0 + 8 vào X1
```

Trong ví dụ trên, lệnh `ADRP` tính địa chỉ cơ bản của `my_array` và lưu vào thanh ghi `X0`, sau đó lệnh `LDR` sẽ load giá trị tại địa chỉ `X0 + 8` vào thanh ghi `X1`.

- 13. **B.EQ and B.GT**: Trong hợp ngữ ARM, các lệnh như `B.EQ` và `B.GT` đều là các lệnh nhảy có điều kiện (conditional branch instructions) và dùng để điều khiển luồng thực thi của chương trình dựa trên các điều kiện cụ thể.

`B.EQ`: Đây là lệnh nhảy có điều kiện "Branch if Equal". Nếu điều kiện bằng (equal) được đáp ứng, chương trình sẽ nhảy đến địa chỉ được chỉ định. Điều kiện này thường được kiểm tra bằng cách so sánh các thanh ghi.

`B.GT`: Đây là lệnh nhảy có điều kiện "Branch if Greater Than". Nếu điều kiện "lớn hơn" được đáp ứng, chương trình sẽ nhảy đến địa chỉ được chỉ định. Điều kiện này thường được kiểm tra bằng cách so sánh các thanh ghi.

Cú pháp chung của các lệnh nhảy có điều kiện trong ARM:

```bash
B{condition} label
```

Trong đó:

- `{condition}` là điều kiện dựa trên kết quả của các lệnh trước đó (ví dụ: `EQ` cho "bằng", `GT` cho "lớn hơn").
- `label` là nhãn (label) của địa chỉ mục tiêu.

Ví dụ sử dụng:

```bash
CMP R0, R1    ; So sánh R0 và R1
BEQ label     ; Nếu R0 bằng R1, thì nhảy đến label
BGT label     ; Nếu R0 lớn hơn R1, thì nhảy đến label
```

Trong ví dụ trên, nếu kết quả của so sánh `CMP` thỏa mãn điều kiện, chương trình sẽ nhảy đến địa chỉ được chỉ định bởi `label`.

- 14. **Shift Instructions**

These instructions perform bitwise shifts on register values.

**LSL (Logical Shift Left)**

```bash
LSL Rd, Rn, #imm
```

Shifts the value in register `Rn` left by the number of bits specified by `imm`, and stores the result in register `Rd`.

**LSR (Logical Shift Right)**

```bash
LSR Rd, Rn, #imm
```

Shifts the value in register `Rn` right by the number of bits specified by `imm`, and stores the result in register `Rd`.

### 2.4 Gadget Exploits in ARM

> Về gadget pop <rg> ; ret

```bash
ldr x1, [sp, #0]
ldp x29, x30, [sp]
add sp, sp, #0x10
```

Các lệnh này xuất hiện trong ngôn ngữ hợp ngữ ARM64 (AArch64). Đây là một phân tích ngắn gọn về mỗi lệnh:

1. `"ldr x1, [sp, #0]\n\t"`:

   - Lệnh `ldr` (load register) được sử dụng để nạp giá trị từ bộ nhớ vào thanh ghi.
   - Trong trường hợp này, giá trị tại địa chỉ con trỏ xếp ngăn (stack pointer - SP) đang trỏ tới được nạp vào thanh ghi x1. `[sp, #0]` có nghĩa là địa chỉ tại ngăn đầu tiên từ xếp ngăn (địa chỉ hiện tại của SP).
   - `\n\t` chỉ đơn giản là ký tự xuống dòng và tab, thường được sử dụng để đảm bảo rằng lệnh sau đó sẽ bắt đầu từ vị trí được căn chỉnh.

2. `"ldp x29, x30, [sp], #0\n\t"`:

   - Lệnh `ldp` (load pair) được sử dụng để nạp giá trị từ bộ nhớ vào cặp thanh ghi.
   - Trong trường hợp này, giá trị từ địa chỉ con trỏ xếp ngăn (hiện tại của SP) được nạp vào thanh ghi x29 và x30. `[sp], #0` có nghĩa là địa chỉ tại ngăn đầu tiên từ xếp ngăn được nạp vào các thanh ghi, sau đó SP sẽ tăng lên để trỏ tới ngăn tiếp theo.
   - `\n\t` là ký tự xuống dòng và tab, tương tự như trên.

3. `"add sp, sp, #0x10"`:
   - Lệnh `add` được sử dụng để thực hiện phép cộng.
   - Trong trường hợp này, giá trị của SP sẽ được cộng thêm `0x10` (16 trong hệ thập phân) để tăng địa chỉ của xếp ngăn. Điều này đồng nghĩa với việc giảm kích thước của xếp ngăn đi 16 byte.

- Tổng cộng, các lệnh trên có thể được tóm tắt thành việc nạp giá trị từ xếp ngăn vào thanh ghi x1, khôi phục các giá trị từ xếp ngăn vào các thanh ghi x29 và x30, và sau đó điều chỉnh xếp ngăn để trỏ tới ngăn tiếp theo.

> Lệnh pop một thanh ghi nào đó trong ARM = ?

Trong kiến trúc ARM, để "pop" (lấy giá trị ra khỏi ngăn xếp) một thanh ghi, bạn thực hiện các bước sau:

1. Sử dụng lệnh `LDR` hoặc `LDM` để lấy giá trị từ ngăn xếp và đưa vào thanh ghi mong muốn.
2. Sau đó, cần điều chỉnh con trỏ xếp ngăn (stack pointer - SP) bằng cách cộng hoặc trừ một giá trị tùy thuộc vào việc bạn đang thao tác với xếp ngăn từ trên xuống hay từ dưới lên.

Ví dụ, để pop giá trị vào thanh ghi R0, bạn có thể sử dụng lệnh như sau (sử dụng AAPCS - ARM Architecture Procedure Call Standard):

```bash
LDR R0, [SP], #4
```

Trong đó:

- `LDR R0, [SP], #4` lấy giá trị từ đỉnh xếp ngăn và đưa vào thanh ghi R0. Sau đó, con trỏ xếp ngăn (SP) được tăng lên 4 byte (#4).
- `#4` đại diện cho kích thước của dữ liệu bạn muốn lấy từ xếp ngăn.

Lưu ý rằng việc sử dụng lệnh pop trong ARM sẽ tùy thuộc vào kiến trúc và các quy tắc cụ thể của mã nguồn bạn đang làm việc. Hãy xem tài liệu hướng dẫn của ARM hoặc trình biên dịch mà bạn đang sử dụng để biết cách sử dụng chính xác.

## 3. Resource References

- https://azeria-labs.com/writing-arm-assembly-part-1/

> Decompiling raw shellcode directly to ARM assembly using online tools might not always be straightforward because most online decompilers expect full binaries rather than raw shellcode. However, you can use local tools and methods to convert the hex shellcode into ARM assembly. Here are some options you can consider:

### 1. **Using GDB (GNU Debugger) with ARM Emulation:**

You can use GDB with ARM architecture settings to disassemble the shellcode. First, create a binary file with the shellcode and then use GDB to disassemble it:

```bash
echo -ne "\x01\x60\x8f\xe2\x16\xff\x2f\xe1\x01\xb5\x92\x1a\x10\x1c\xf0\x46\x02\x4a\x90\x47\x02\x4a\x1c\x32\x90\x47\x01\xbd\x24\xf9\x03\x80\x50\xf5\x03\x80" > shellcode.bin
```

Now, run GDB with ARM support:

```bash
gdb -q --nh
(gdb) set architecture arm
(gdb) target sim
(gdb) disassemble /r shellcode.bin
```

### 2. **Using Python with Capstone and Pwntools:**

You can write a Python script using Capstone (a lightweight multi-platform, multi-architecture disassembly framework) and Pwntools to disassemble ARM shellcode. Here's how you can do it:

```python
from capstone import *
from pwn import *

# Define the shellcode
shellcode = b"\x01\x60\x8f\xe2\x16\xff\x2f\xe1\x01\xb5\x92\x1a\x10\x1c\xf0\x46\x02\x4a\x90\x47\x02\x4a\x1c\x32\x90\x47\x01\xbd\x24\xf9\x03\x80\x50\xf5\x03\x80"

# Initialize Capstone disassembler for ARM
md = Cs(CS_ARCH_ARM, CS_MODE_ARM)

# Disassemble the shellcode
for instr in md.disasm(shellcode, 0x1000):
    print(f"0x{instr.address:x}:\t{instr.mnemonic}\t{instr.op_str}")
```

You can run this script locally to see the disassembled ARM instructions.

### 3. **Using Online Disassembly Tools:**

- **Online Disassemblers:** Some online platforms support ARM assembly disassembly from raw shellcode. These tools include [godbolt.org](https://godbolt.org/) (Compiler Explorer) and [onlinedisassembler.com](https://onlinedisassembler.com/odaweb/). However, their capabilities might be limited, and you often need to work with full binaries or object files rather than raw shellcode.

### 4. **Manual Decompilation:**

If the methods above are not suitable, another approach is to manually load the shellcode into an ARM disassembler like IDA Pro or Radare2:

- **IDA Pro:** Load the shellcode into IDA Pro, set the architecture to ARM, and then use the disassembly view to convert the shellcode into assembly.
- **Radare2:** Use Radare2 with ARM mode to disassemble the shellcode. You can use the following commands in Radare2:

  ```bash
  r2 -a arm -b 32 -D esil -e asm.arch=arm -e asm.bits=32 -e io.cache=true shellcode.bin
  ```

### Summary:

- While there isn't a straightforward online tool for decompiling shellcode into ARM assembly, the above methods using GDB, Capstone, and local disassemblers like IDA Pro or Radare2 should work effectively.
