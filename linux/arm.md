# ARM

ARM is a family of Reduced Instruction Set Computer (RISC) architectures developed and licensed by Arm Holdings. Rather than manufacturing chips, Arm licenses its ISA and core designs to partners (Apple, Qualcomm, Samsung, etc.) who produce the actual silicon. This licensing model has made ARM the dominant architecture for mobile, embedded, and increasingly server and desktop workloads.

## Architecture versions

| Version | Bits | ABI | Common name | Notes |
|---------|------|-----|-------------|-------|
| ARMv6 | 32 | armhf | ARM11 | Raspberry Pi 1, Zero |
| ARMv7-A | 32 | armhf / armel | Cortex-A | Most 32-bit SBCs |
| ARMv8-A | 64+32 | aarch64 | Cortex-A (64-bit) | Current standard |
| ARMv9-A | 64 | aarch64 | Cortex-A (v9) | SVE2, security features |
| ARMv8-M | 32 | - | Cortex-M | Microcontrollers |
| ARMv8-R | 32/64 | - | Cortex-R | Real-time systems |

**Profiles:**
- **A** (Application) - high-performance, runs full OS (Linux, Android, macOS)
- **R** (Real-time) - deterministic latency, automotive, industrial
- **M** (Microcontroller) - low power, bare-metal embedded (Arduino, STM32)

## ABI and naming

The naming of ARM ABIs can be confusing:

| Name | Meaning |
|------|---------|
| `armel` | ARMv4T+, software float, little-endian |
| `armhf` | ARMv7+, hard-float (VFPv3), little-endian |
| `aarch64` / `arm64` | ARMv8-A 64-bit |

Most modern Linux distributions target `aarch64`. Use `armhf` only for older 32-bit boards.

## Registers (AArch64)

| Registers | Purpose |
|-----------|---------|
| `x0`–`x7` | Function arguments / return values |
| `x8` | Indirect result location / syscall number |
| `x9`–`x15` | Temporary (caller-saved) |
| `x16`–`x17` | Intra-procedure-call scratch |
| `x18` | Platform register |
| `x19`–`x28` | Callee-saved |
| `x29` | Frame pointer (FP) |
| `x30` | Link register (LR) - return address |
| `sp` | Stack pointer |
| `pc` | Program counter |
| `w0`–`w30` | 32-bit view of `x0`–`x30` |

## Key instructions (AArch64)

```asm
// Data movement
mov x0, #42          // x0 = 42
mov x1, x0           // x1 = x0
ldr x0, [x1]         // x0 = memory[x1]
str x0, [x1]         // memory[x1] = x0
ldr x0, [x1, #8]     // x0 = memory[x1 + 8]

// Arithmetic
add x0, x1, x2       // x0 = x1 + x2
sub x0, x1, x2       // x0 = x1 - x2
mul x0, x1, x2       // x0 = x1 * x2
lsl x0, x1, #2       // x0 = x1 << 2 (multiply by 4)
lsr x0, x1, #1       // x0 = x1 >> 1 (unsigned divide by 2)

// Logical
and x0, x1, x2
orr x0, x1, x2
eor x0, x1, x2       // XOR
bic x0, x1, x2       // AND NOT

// Branches
b   label             // unconditional branch
bl  label             // branch with link (call)
ret                   // return (branch to x30)
cbz x0, label         // branch if x0 == 0
cbnz x0, label        // branch if x0 != 0
b.eq / b.ne / b.lt / b.gt  // conditional branches after cmp

// Compare
cmp x0, x1           // sets flags, discards result
```

## Hello world (AArch64 Linux)

```asm
.section .data
msg:    .ascii "Hello, ARM!\n"
len = . - msg

.section .text
.global _start
_start:
    mov x8, #64        // syscall: write
    mov x0, #1         // fd: stdout
    adr x1, msg        // buffer
    mov x2, #len       // length
    svc #0

    mov x8, #93        // syscall: exit
    mov x0, #0         // status
    svc #0
```

```bash
as -o hello.o hello.s
ld -o hello hello.o
./hello
```

## Toolchain

There are two distinct toolchain families for ARM. Choosing the wrong one is a common source of confusion:

| Toolchain prefix | Target | Use case |
|-----------------|--------|---------|
| `arm-none-eabi-` | Bare-metal (no OS) | Cortex-M MCUs, custom firmware, synthesizer units |
| `arm-linux-gnueabihf-` | Linux userspace (32-bit) | armhf SBCs, cross-compiling for Raspberry Pi 3 |
| `aarch64-linux-gnu-` | Linux userspace (64-bit) | aarch64 SBCs, servers |

### Linux cross-compilation (from x86_64)

```bash
# Install cross toolchains
sudo apt install gcc-aarch64-linux-gnu binutils-aarch64-linux-gnu   # 64-bit
sudo apt install gcc-arm-linux-gnueabihf binutils-arm-linux-gnueabihf  # 32-bit

# Cross-compile a C program
aarch64-linux-gnu-gcc -o hello hello.c
arm-linux-gnueabihf-gcc -o hello hello.c

# Cross-compile with Rust
rustup target add aarch64-unknown-linux-gnu
cargo build --target aarch64-unknown-linux-gnu
```

### Bare-metal (`arm-none-eabi`)

The `arm-none-eabi` toolchain targets ARM with no OS and no standard C library (or a minimal one like newlib). This is the standard for Cortex-M microcontrollers, custom firmware, and synthesizer plugin development.

```bash
sudo apt install gcc-arm-none-eabi binutils-arm-none-eabi
```

Typical compiler flags depend on the exact core:

```bash
# Cortex-M4 with FPU (e.g. STM32F4, KORG logue hardware)
arm-none-eabi-gcc \
  -mcpu=cortex-m4 \
  -mthumb \
  -mfpu=fpv4-sp-d16 \
  -mfloat-abi=hard \
  -Os -fno-exceptions \
  -o firmware.elf main.c

# Cortex-M0+ (no FPU, smaller core)
arm-none-eabi-gcc \
  -mcpu=cortex-m0plus \
  -mthumb \
  -mfloat-abi=soft \
  -Os \
  -o firmware.elf main.c
```

Key differences from a Linux cross-compile:
- No `libc` by default - `printf`, `malloc` etc. require linking [[development/newlib|newlib]] and implementing syscall stubs
- No OS syscalls - everything goes through hardware registers or a vendor HAL
- Needs a **linker script** to place `.text`, `.data`, `.bss` at correct flash/RAM addresses
- Needs a **startup file** (`startup.s` or `startup.c`) to set up the stack and call `main`

Minimal bare-metal linker script:

```ld
MEMORY {
    FLASH (rx)  : ORIGIN = 0x08000000, LENGTH = 256K
    SRAM  (rwx) : ORIGIN = 0x20000000, LENGTH = 64K
}

SECTIONS {
    .text : { *(.text*) *(.rodata*) } > FLASH
    .data : { *(.data*) } > SRAM AT > FLASH
    .bss  : { *(.bss*) *(COMMON) } > SRAM
}
```

### QEMU user-mode (run ARM binaries on x86)

```bash
sudo apt install qemu-user-static binfmt-support
qemu-aarch64-static ./hello
```

### QEMU system emulation

```bash
qemu-system-aarch64 \
  -M virt \
  -cpu cortex-a57 \
  -m 2G \
  -kernel Image \
  -append "console=ttyAMA0 root=/dev/vda" \
  -drive if=virtio,file=rootfs.img \
  -nographic
```

## Linux distributions

| Distribution | aarch64 | armhf | Notes |
|-------------|---------|-------|-------|
| Fedora | ✓ | ✓ | Tier 1 since Fedora 28 |
| Ubuntu | ✓ | ✓ | Excellent SBC support |
| Debian | ✓ | ✓ | armel + armhf + arm64 |
| Arch Linux ARM | ✓ | ✓ | `archlinuxarm.org` |
| Alpine | ✓ | ✓ | Great for containers |
| openSUSE | ✓ | - | |

## Common hardware

| Board / Device | SoC | Architecture |
|----------------|-----|-------------|
| Raspberry Pi 4/5 | BCM2711/2712 | ARMv8 Cortex-A72/A76 |
| Raspberry Pi 3 | BCM2837 | ARMv8 Cortex-A53 |
| Raspberry Pi Zero 2 W | RP3A0 | ARMv8 Cortex-A53 |
| Apple M1–M4 | Apple Silicon | ARMv8.5-A |
| Snapdragon X Elite | Oryon | ARMv9 |
| NVIDIA Jetson | Tegra | ARMv8 Cortex-A57/A78 |
| Ampere Altra | Neoverse N1 | ARMv8.2 (server) |

## Containers

Multi-arch images are the standard approach - no need to distinguish ARM images manually if the registry has a manifest list.

```bash
# Pull natively on ARM host (automatic)
docker pull ubuntu:24.04

# Build multi-arch image from x86 host
docker buildx create --use
docker buildx build --platform linux/amd64,linux/arm64 -t myimage:latest --push .

# Inspect available platforms
docker buildx imagetools inspect ubuntu:24.04
```

Run x86 containers on ARM with emulation (slower):

```bash
docker run --platform linux/amd64 ubuntu:24.04 uname -m
```

## Logue SDK (synthesizer unit development)

KORG's [logue-sdk](https://github.com/korginc/logue-sdk) allows developing custom oscillators and effects for logue-series synthesizers. The hardware is ARM Cortex-M4F based; units are compiled with `arm-none-eabi-gcc` and uploaded via KORG Librarian or the NTS-1 mk2 web app.

### Supported platforms

| Platform | Unit file | Core |
|----------|-----------|------|
| minilogue xd | `.mnlgxdunit` | Cortex-M4F |
| prologue | `.prlgunit` | Cortex-M4F |
| NTS-1 mk1 | `.ntkdigunit` | Cortex-M4F |
| NTS-1 mk2 | `.nts1mkiiunit` | Cortex-M7F |

### Unit types

| Type | Description |
|------|-------------|
| `osc` | Custom oscillator - generates audio signal |
| `modfx` | Modulation effect (chorus, flanger, etc.) |
| `delfx` | Delay effect |
| `revfx` | Reverb effect |

### Toolchain setup

```bash
# Install arm-none-eabi toolchain
sudo apt install gcc-arm-none-eabi binutils-arm-none-eabi

# Clone the SDK
git clone https://github.com/korginc/logue-sdk.git
cd logue-sdk

# Initialise submodules and toolchain
git submodule update --init --recursive
```

### Build flags (from the SDK Makefile)

```makefile
MCU     = cortex-m4
MCFLAGS = -mcpu=$(MCU) -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard
CFLAGS  = $(MCFLAGS) -Os -fno-exceptions -fno-non-call-exceptions \
          -ffast-math -fsingle-precision-constant
```

### Unit structure

A minimal oscillator unit exposes three functions the firmware calls into:

```c
#include "userosc.h"

void OSC_INIT(uint32_t platform, uint32_t api) {
    // Called once at load time
}

void OSC_CYCLE(const user_osc_param_t *const params,
               int32_t *yn, const uint32_t frames) {
    // Called every audio block (frames = 64 samples typically)
    // Fill yn[] with Q31 audio samples
}

void OSC_NOTEON(const user_osc_param_t *const params) { }
void OSC_NOTEOFF(const user_osc_param_t *const params) { }

void OSC_PARAM(uint16_t index, uint16_t value) {
    // Handle parameter knob changes (value 0–1023)
}
```

### Building and deploying

```bash
# Build a unit (from its directory)
cd logue-sdk/platform/minilogue-xd/osc/waves
make

# Output: build/waves.mnlgxdunit
# Upload via KORG Librarian (desktop) or drag-and-drop (NTS-1 mk2)
```

### Move / Schwung effects

Move and Schwung are custom logue units. See [[development/audio/schwung-move-effects]] for implementation notes.

## NEON (SIMD)

> Not yet documented. NEON is ARM's SIMD extension for Cortex-A (not Cortex-M). Used for DSP, ML inference, and multimedia workloads on application processors.

## Resources

- [Arm Architecture Reference Manual (Arm ARM)](https://developer.arm.com/documentation/ddi0487/)
- [Arm Learning Paths](https://learn.arm.com/)
- [Arm Developer](https://developer.arm.com/)
- [Raspberry Pi documentation](https://www.raspberrypi.com/documentation/)
- [KORG logue-sdk](https://github.com/korginc/logue-sdk)
- [logue-sdk unit development guide](https://github.com/korginc/logue-sdk/tree/master/docs)
