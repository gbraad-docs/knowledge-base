# Newlib

Newlib is a C standard library implementation designed for embedded and bare-metal targets - systems with no operating system. It is the default libc bundled with the `arm-none-eabi` and `riscv32-unknown-elf` (and similar ELF) toolchains, replacing glibc which assumes a full POSIX OS beneath it.

## Why newlib exists

A standard C library needs OS services for things like:
- Writing output (`printf` → `write` syscall)
- Allocating heap memory (`malloc` → `sbrk`/`brk` syscall)
- Exiting (`exit` → `_exit` syscall)
- Opening files (`fopen` → `open` syscall)

On bare-metal there are no syscalls. Newlib solves this by splitting the library into two layers:
- **High-level portable code** - `printf`, `malloc`, `memcpy`, `strlen`, etc.
- **Low-level syscall stubs** - thin functions you must implement to bridge newlib to your hardware

This bridging is called **retargeting**.

## Two variants

| Variant | Spec flag | Size | Use case |
|---------|-----------|------|---------|
| newlib | `--specs=nosys.specs` | Larger | Full features, more flash/RAM |
| newlib-nano | `--specs=nano.specs` | Much smaller | Space-constrained MCUs |

**newlib-nano** replaces the standard `printf`/`scanf` with smaller versions that drop support for floating-point format specifiers by default (add `--specs=nano.specs -u _printf_float` to restore them).

## Syscall stubs you must implement

Newlib calls these functions; you provide the implementation for your hardware:

| Stub | Purpose | Minimal implementation |
|------|---------|----------------------|
| `_write` | Output characters (used by `printf`) | Write to UART |
| `_read` | Read input | Read from UART |
| `_sbrk` | Grow heap (used by `malloc`) | Move heap pointer |
| `_exit` | Program termination | Infinite loop or reset |
| `_close`, `_fstat`, `_isatty`, `_lseek` | File operations | Return `-1` / stub |

Minimal retarget for a UART-based target:

```c
#include <sys/stat.h>
#include <errno.h>

// UART transmit - implement for your hardware
extern void uart_putchar(char c);

int _write(int fd, char *buf, int len) {
    for (int i = 0; i < len; i++) {
        uart_putchar(buf[i]);
    }
    return len;
}

// Heap: grow from end of .bss toward stack
extern char _end;           // linker symbol: end of .bss
static char *heap_ptr = &_end;

void *_sbrk(int incr) {
    char *prev = heap_ptr;
    heap_ptr += incr;
    return (void *)prev;
}

// Minimal stubs - just enough to link
int  _close(int fd)                        { return -1; }
int  _fstat(int fd, struct stat *st)       { st->st_mode = S_IFCHR; return 0; }
int  _isatty(int fd)                       { return 1; }
int  _lseek(int fd, int offset, int whence){ return 0; }
int  _read(int fd, char *buf, int len)     { return 0; }
void _exit(int status)                     { while (1); }
```

## Linking with newlib

### arm-none-eabi

```bash
# newlib-nano + stub syscalls provided by -lnosys (all return -1/ENOSYS)
arm-none-eabi-gcc \
  -mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard \
  --specs=nano.specs --specs=nosys.specs \
  -T linker.ld -o firmware.elf startup.s retarget.c main.c

# newlib-nano + your own retarget.c (omit --specs=nosys.specs)
arm-none-eabi-gcc \
  -mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard \
  --specs=nano.specs \
  -T linker.ld -o firmware.elf startup.s retarget.c main.c -lc -lm
```

### riscv32-unknown-elf

```bash
# newlib-nano + stub syscalls
riscv32-unknown-elf-gcc \
  -march=rv32im -mabi=ilp32 \
  --specs=nano.specs --specs=nosys.specs \
  -T linker.ld -o program.elf startup.s retarget.c main.c

# Your own retarget
riscv32-unknown-elf-gcc \
  -march=rv32im -mabi=ilp32 \
  --specs=nano.specs \
  -T linker.ld -o program.elf startup.s retarget.c main.c -lc -lm
```

## `--specs` flags reference

| Flag | Effect |
|------|--------|
| `--specs=nano.specs` | Link newlib-nano (smaller printf/malloc) |
| `--specs=nosys.specs` | Link pre-built stub syscalls (all return error) |
| `--specs=rdimon.specs` | Link semihosting syscalls (see below) |

## Semihosting

Semihosting is an alternative to retargeting: instead of routing `printf` to a UART, it tunnels the output back through the debugger (JTAG/SWD) to the host terminal. Useful during development - no UART required.

```bash
arm-none-eabi-gcc ... --specs=rdimon.specs -lrdimon
```

In QEMU:

```bash
qemu-system-arm -M mps2-an385 -semihosting -kernel firmware.elf
```

**Do not** ship firmware built with `rdimon.specs` - it will hang without a debugger attached.

## What newlib provides (without retargeting)

Even without implementing any syscalls, you get the full non-OS-dependent portion of libc:

- `string.h` - `memcpy`, `memset`, `strlen`, `strcmp`, `strcpy`, …
- `stdlib.h` - `atoi`, `strtol`, `abs`, `qsort`, `bsearch`
- `math.h` - `sin`, `cos`, `sqrt`, `fabs`, … (link with `-lm`)
- `stdint.h`, `stdbool.h`, `stddef.h` - type definitions
- `printf` / `sprintf` - works without `_write` if you only use `sprintf` to format into a buffer

`malloc`/`free` require `_sbrk`. `printf` to stdout requires `_write`.

## Linker script considerations

Newlib's `_sbrk` needs a `_end` symbol marking the end of statically allocated data (the start of the heap). Add it to your linker script:

```ld
.bss : {
    _bss_start = .;
    *(.bss*) *(COMMON)
    _bss_end = .;
    _end = .;        /* newlib heap starts here */
} > RAM

.heap (NOLOAD) : {
    . = ALIGN(8);
    . += 4K;         /* reserve heap space */
} > RAM

.stack (NOLOAD) : {
    . = ALIGN(8);
    _stack_top = . + 8K;
} > RAM
```

## Resources

- [Newlib source](https://sourceware.org/newlib/)
- [newlib-nano announcement](https://community.arm.com/arm-community-blogs/b/tools-software-ides-blog/posts/shrink-your-mcu-code-size-with-gcc-arm-embedded-4-7)
- [Retargeting guide (Arm)](https://developer.arm.com/documentation/100748/latest/Retargeting-Arm-C-Library-Functions)
