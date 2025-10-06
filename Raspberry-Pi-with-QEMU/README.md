# Raspberry-Pi-with-QEMU

- https://azeria-labs.com/emulate-raspberry-pi-with-qemu/

- Setting up QEMU for ARM - Download:

  - `Kernel raspberrypi img`: https://downloads.raspberrypi.org/raspbian/images/raspbian-2017-04-10/

  - `Driver support qemu`: https://github.com/dhruvvyas90/qemu-rpi-kernel

```bash
sudo apt-get install qemu-system

sudo apt update
sudo apt install qemu qemu-system-arm
```

- Command run qemu ARM:

```bash
qemu-system-arm -kernel ./kernel-qemu-4.4.34-jessie -cpu arm1176 -m 256 -M versatilepb -serial stdio -append "root=/dev/sda2 rootfstype=ext4 rw" -hda ./2017-04-10-raspbian-jessie.img -nic user, hostfwd=tcp::5022-:22 -no-reboot
```

- In virtual machine ARM qemu (raspbian computer), open terminal:

```bash
sudo service ssh start
```

- In Ubuntu run command conncet ssh (pasword: raspberry):

```bash
ssh pi@127.0.0.1 -p 5022
```

- File `hello.s`:

```bash
.global _start
_start:
	MOV R0, #1
	LDR R1, =message
	LDR R2, =len
	MOV R7, #4
	SWI 0

	MOV R7, #1
	SWI 0

_data
message:
	.asciz "hello world\n"
len = .-message
```

- Compile:

```bash
as hello.s -o hello.o

ld hello.o - o hello
```

- File Execve(b"/bin/sh", 0, 0):

```bash
section .text
.global _start
_start:
	ADD R0, PC, #12
	LDR R1, #0
	LDR R2, #0
	MOV R7, #11
	SVC #0

.ascii "/bin/sh\0"
```

```c
int main(){
	system("/bin/sh");
}
```
