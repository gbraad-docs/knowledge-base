# RISC-V

RISC-V (pronounced "risk five") is an open, free ISA (Instruction Set Architecture) developed at UC Berkeley starting in 2010. Unlike ARM or x86, nobody owns RISC-V - it is governed by RISC-V International, a non-profit with hundreds of member companies. Anyone can implement it without paying royalties or signing NDAs.

This openness has made RISC-V attractive for research, custom silicon, embedded systems, and increasingly for general-purpose computing.

## ISA structure

RISC-V is modular: a small mandatory base plus optional standard extensions.

### Base ISAs

| Name | Width | Description |
|------|-------|-------------|
| RV32I | 32-bit | Integer base - minimum viable ISA |
| RV64I | 64-bit | 64-bit integers, 32-bit compatibility mode |
| RV128I | 128-bit | Future use |

### Standard extensions

| Letter | Extension | Notes |
|--------|-----------|-------|
| M | Integer Multiply/Divide | `mul`, `div`, `rem` |
| A | Atomic instructions | `lr`, `sc`, `amo*` |
| F | Single-precision float | 32-bit IEEE 754 |
| D | Double-precision float | 64-bit IEEE 754, requires F |
| C | Compressed instructions | 16-bit encodings, reduces code size ~25% |
| V | Vector | SIMD-style operations |
| B | Bit manipulation | |
| H | Hypervisor | |
| Zicsr | Control & Status Registers | Required for OS |
| Zifencei | Instruction-fetch fence | Required for self-modifying code |

**G = IMAFD + Zicsr + Zifencei** - the general-purpose combination used for Linux systems.

A target is described by combining letters: `rv64gc` means 64-bit + G + Compressed.

## Registers

RISC-V has 32 integer registers (+ 32 float registers with F/D extension):

| Register | ABI name | Convention |
|----------|----------|-----------|
| x0 | `zero` | Hardwired zero - writes ignored |
| x1 | `ra` | Return address |
| x2 | `sp` | Stack pointer |
| x3 | `gp` | Global pointer |
| x4 | `tp` | Thread pointer |
| x5–x7 | `t0`–`t2` | Temporaries (caller-saved) |
| x8 | `s0` / `fp` | Saved / frame pointer (callee-saved) |
| x9 | `s1` | Saved (callee-saved) |
| x10–x11 | `a0`–`a1` | Args / return values |
| x12–x17 | `a2`–`a7` | Arguments |
| x18–x27 | `s2`–`s11` | Saved (callee-saved) |
| x28–x31 | `t3`–`t6` | Temporaries (caller-saved) |

## Key instructions (RV64I)

```asm
# Data movement
li   a0, 42          # load immediate (pseudo, expands to lui+addi)
mv   a0, a1          # move register (pseudo: addi a0, a1, 0)
la   a0, label       # load address (pseudo)
ld   a0, 0(sp)       # load doubleword from memory
sd   a0, 0(sp)       # store doubleword to memory
lw   a0, 0(sp)       # load word (32-bit, sign-extended)
sw   a0, 0(sp)       # store word

# Arithmetic
add  a0, a1, a2      # a0 = a1 + a2
addi a0, a1, 10      # a0 = a1 + 10 (immediate)
sub  a0, a1, a2      # a0 = a1 - a2
mul  a0, a1, a2      # a0 = a1 * a2 (M extension)
div  a0, a1, a2      # a0 = a1 / a2 (M extension)

# Logical
and  a0, a1, a2
or   a0, a1, a2
xor  a0, a1, a2
sll  a0, a1, a2      # shift left logical
srl  a0, a1, a2      # shift right logical
sra  a0, a1, a2      # shift right arithmetic

# Branches (compare-and-branch, no flags register)
beq  a0, a1, label   # branch if a0 == a1
bne  a0, a1, label   # branch if a0 != a1
blt  a0, a1, label   # branch if a0 < a1 (signed)
bltu a0, a1, label   # branch if a0 < a1 (unsigned)
bge  a0, a1, label   # branch if a0 >= a1 (signed)
bgeu a0, a1, label   # branch if a0 >= a1 (unsigned)

# Jumps
jal  ra, label       # jump and link (call)
jalr ra, 0(ra)       # jump and link register (used for ret)
ret                  # return (pseudo: jalr zero, 0(ra))
```

## Hello world (RV64 Linux)

```asm
.section .data
msg:    .string "Hello, RISC-V!\n"
len = . - msg

.section .text
.global _start
_start:
    li   a7, 64         # syscall: write
    li   a0, 1          # fd: stdout
    la   a1, msg        # buffer address
    li   a2, len        # length
    ecall

    li   a7, 93         # syscall: exit
    li   a0, 0          # status
    ecall
```

```bash
riscv64-linux-gnu-as -o hello.o hello.s
riscv64-linux-gnu-ld -o hello hello.o
./hello   # on RISC-V hardware or QEMU
```

## Key differences from ARM/x86

| Feature | RISC-V | ARM (AArch64) | x86-64 |
|---------|--------|---------------|--------|
| Registers | 32 int | 31 int + SP | 16 |
| Flags register | No | Yes (NZCV) | Yes |
| Branches | Compare-and-branch | Flags-based | Flags-based |
| Instruction size | 32-bit (+ 16-bit with C) | 32-bit (+ 16-bit Thumb) | Variable 1–15 bytes |
| ISA ownership | Open | Arm Holdings | Intel/AMD |
| Royalties | None | Yes | Yes |

Notable: RISC-V has **no flags register**. All branches explicitly compare two registers, making pipelines simpler.

## Toolchain

As with ARM, there are two toolchain families:

| Toolchain prefix | Target | Use case |
|-----------------|--------|---------|
| `riscv32-unknown-elf-` | Bare-metal 32-bit | Microcontrollers, custom emulators, no OS |
| `riscv64-linux-gnu-` | Linux userspace 64-bit | SBCs, servers, standard OS development |

### Linux cross-compilation (RV64)

```bash
# Debian/Ubuntu
sudo apt install gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu

# Cross-compile C
riscv64-linux-gnu-gcc -march=rv64gc -mabi=lp64d -o hello hello.c

# Rust
rustup target add riscv64gc-unknown-linux-gnu
cargo build --target riscv64gc-unknown-linux-gnu
```

### ABI naming

| ABI | Float | Integer |
|-----|-------|---------|
| `lp64` | None (software float) | 64-bit |
| `lp64f` | F (single) in FP regs | 64-bit |
| `lp64d` | D (double) in FP regs | 64-bit |

`lp64d` is standard for Linux on capable hardware.

### Bare-metal (`riscv32-unknown-elf`)

For bare-metal RV32 targets (no OS, no libc) use the ELF toolchain:

```bash
sudo apt install gcc-riscv64-linux-gnu   # includes riscv32 multilib on some distros
# Or build from source / use a pre-built release:
# https://github.com/riscv-collab/riscv-gnu-toolchain

# Compile bare-metal RV32IM
riscv32-unknown-elf-gcc \
  -march=rv32im \
  -mabi=ilp32 \
  -nostdlib \
  -T linker.ld \
  -o program.elf startup.s main.c

# Extract raw binary (no ELF headers)
riscv32-unknown-elf-objcopy -O binary program.elf program.bin
```

Key flags:
- `-march=rv32im` - RV32 base + M extension (no float, no C)
- `-mabi=ilp32` - 32-bit integers, software float, 32-bit pointers
- `-nostdlib` - no libc, no startup files; drop this and add `--specs=nano.specs` to use [[development/newlib|newlib]]
- `-T linker.ld` - provide your own memory layout

## QEMU emulation

```bash
sudo apt install qemu-system-riscv64 qemu-user-static

# User-mode (run a single binary)
qemu-riscv64-static ./hello

# System emulation with OpenSBI + U-Boot
qemu-system-riscv64 \
  -M virt \
  -m 2G \
  -smp 4 \
  -bios /usr/lib/riscv64-linux-gnu/opensbi/generic/fw_jump.bin \
  -kernel u-boot.bin \
  -drive file=rootfs.img,format=raw,id=hd0 \
  -device virtio-blk-device,drive=hd0 \
  -nographic
```

## Linux distributions

| Distribution | Status | Notes |
|-------------|--------|-------|
| Fedora | ✓ Tier 2 | Official since Fedora 38 |
| Ubuntu | ✓ | 22.04+ supports riscv64 |
| Debian | ✓ | `ports.debian.org/debian-ports` |
| openSUSE | ✓ | Tumbleweed |
| Alpine | ✓ | Good container base |
| Arch Linux RISC-V | ✓ | Community port |

## Hardware

### Development boards

| Board | SoC | CPU cores | Notes |
|-------|-----|-----------|-------|
| SiFive HiFive Unmatched | FU740 | 4× U74 RV64GC | First desktop-class RISC-V |
| StarFive VisionFive 2 | JH7110 | 4× U74 RV64GC | Popular, affordable |
| Milk-V Pioneer | SG2042 | 64× C920 RV64GCB | High-core-count server |
| Milk-V Duo | CV1800B | C906 RV64GCV | Ultra-low-cost |
| Sipeed LicheePi 4A | TH1520 | 4× C910 RV64GCDXV | Good performance/price |
| PINE64 Star64 | JH7110 | 4× U74 RV64GC | |

### Microcontrollers

| Device | SoC | Notes |
|--------|-----|-------|
| Espressif ESP32-C3/C6 | RISC-V | Wi-Fi/BT, popular for IoT |
| WCH CH32V | RISC-V | Cheap, widely available |
| GigaDevice GD32VF103 | Bumblebee N200 | First RISC-V MCU on mass market |

## Privileged architecture

RISC-V defines three privilege levels:

| Level | Name | Use |
|-------|------|-----|
| M | Machine | Firmware (OpenSBI), full hardware access |
| S | Supervisor | OS kernel |
| U | User | Application code |

**OpenSBI** (Open Source Supervisor Binary Interface) is the standard M-mode firmware, equivalent to UEFI/BIOS on x86 or ATF on ARM.

Boot sequence: `OpenSBI → U-Boot → Linux kernel`

## emu - RV32IM bare-metal workbench

`~/Projects/riscv/emu/` is a self-contained RV32IM emulator and workbench. It is **not** a QEMU wrapper - it is a full software implementation of a RISC-V CPU plus a set of memory-mapped peripherals, with both a native CLI and a WebAssembly build for in-browser use.

### What it implements

- **ISA**: RV32IM - 32-bit integer base + M extension (multiply/divide)
- **Privilege**: Machine mode (M-mode) only - no OS, no MMU
- **Memory**: Configurable 64 KB – 16 MB, little-endian
- **Peripherals** (memory-mapped):

| Peripheral | Base address | Notes |
|-----------|-------------|-------|
| GPIO | `0x10000000` | Input/output, edge/level interrupts |
| UART | `0x10001000` | TX/RX ring buffers |
| I2C | `0x10003000` | PCF8574 expander → 16×2 LCD display |
| Interrupt controller | `0x10005000` | Vectored or direct trap dispatch |

### Memory layout

```
0x00000000  RAM (default 256 KB, configurable)
0x10000000  GPIO
0x10001000  UART
0x10003000  I2C / LCD
0x10005000  Interrupt controller
```

### Build

```bash
cd ~/Projects/riscv/emu

# Native CLI
mkdir build && cd build
cmake .. && cmake --build .
# Produces: build/riscv-emu  build/riscv-as

# WebAssembly (requires Emscripten)
./build-wasm.sh
# Produces: web/emu.js  web/emu.wasm  web/assembler.js
```

### Running programs

```bash
# Assemble with the included Python assembler
./tools/riscv-asm.py examples/fibonacci.s examples/fibonacci.bin

# Run the binary (max 100 instructions)
./build/riscv-emu examples/fibonacci.bin 100

# Run with more memory (1 MB)
./build/riscv-emu examples/fibonacci.bin 0 1024
```

Output shows final register state and the LCD display buffer if used.

### Bare-metal program structure

Every program needs a startup file and a linker script:

**`start.s`** - sets up the stack and clears BSS:

```asm
.section .text._start
.global _start

_start:
    la   sp, _stack_top     # load stack pointer from linker symbol

    # clear BSS
    la   t0, _bss_start
    la   t1, _bss_end
1:  beq  t0, t1, 2f
    sw   zero, 0(t0)
    addi t0, t0, 4
    j    1b
2:
    call main
    ecall                   # signal emulator to stop
```

**`linker.ld`** - memory layout for the emulator:

```ld
MEMORY {
    RAM (rwx) : ORIGIN = 0x00000000, LENGTH = 256K
}

SECTIONS {
    . = 0x00000000;
    .text   : { KEEP(*(.text._start)) *(.text*) } > RAM
    .rodata : { *(.rodata*) }                     > RAM
    .data   : { *(.data*) }                       > RAM
    .bss    : {
        _bss_start = .;
        *(.bss*) *(COMMON)
        _bss_end = .;
    } > RAM
    .stack (NOLOAD) : {
        . = ALIGN(16);
        _stack_top = . + 16K;
    } > RAM
}
```

**Build and run a C program:**

```bash
riscv32-unknown-elf-gcc \
  -march=rv32im -mabi=ilp32 -nostdlib \
  -T linker.ld \
  -o program.elf start.s main.c

riscv32-unknown-elf-objcopy -O binary program.elf program.bin
./build/riscv-emu program.bin
```

### UART output

```c
#define UART_BASE  0x10001000
#define UART_CTRL  (*(volatile uint32_t*)(UART_BASE + 0x08))
#define UART_DATA  (*(volatile uint32_t*)(UART_BASE + 0x00))

void putchar(char c) {
    UART_CTRL = 1;   // enable
    UART_DATA = c;
}
```

### Interrupt handling

```asm
# Install trap vector
la   t0, trap_handler
csrw mtvec, t0          # set machine trap vector

# Enable machine external interrupt + global interrupts
li   t0, 0x800          # MIE.MEIE
csrw mie, t0
li   t0, 0x8            # MSTATUS.MIE
csrw mstatus, t0

trap_handler:
    csrr t0, mcause     # read cause
    # handle ...
    mret                # return from trap (restores MSTATUS.MIE)
```

### WebAssembly / web workbench

The emulator compiles to WASM (~18 KB) and is callable from JavaScript:

```javascript
createRISCVEmu().then(Module => {
    const emu = Module._emu_create(65536);       // 64 KB memory
    Module._emu_load_program(emu, ptr, size);    // load .bin
    Module._emu_run(emu, 1000);                  // run ≤1000 instructions
    const a0 = Module._emu_get_register(emu, 10);
    const lcd = Module.UTF8ToString(Module._emu_get_lcd_line1(emu));
    Module._emu_destroy(emu);
});
```

Open `web/index.html` locally to use the full workbench (editor + assembler + emulator + LCD display).

## Resources

- [RISC-V Specifications](https://riscv.org/specifications/)
- [RISC-V Unprivileged ISA](https://github.com/riscv/riscv-isa-manual)
- [RISC-V International](https://riscv.org/)
- [riscv-software-list](https://github.com/riscv-software-src/riscv-software-list)
- [SiFive docs](https://www.sifive.com/documentation)
- [riscv/emu workbench](~/Projects/riscv/emu/)
