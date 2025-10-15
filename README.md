# Baremetal Embedded System on ARM Cortex-M4 (STM32F4)

### Overview

This project demonstrates building and running a bare-metal embedded system on an ARM Cortex-M4 (STM32F407) microcontroller without using any IDE or HAL libraries.
All compilation, linking, and flashing are performed using GNU ARM Embedded Toolchain, OpenOCD, and GDB, entirely from the command line.

It showcases the complete embedded software bring-up process — from writing the startup code, linker script, and C runtime initialization, to building relocatable object files, managing memory sections, and debugging on target hardware.

---

### Key Features

* Baremetal C programming without RTOS, HAL, or CMSIS dependencies
* Custom startup file implementing vector table, exception handlers, and reset logic
* Custom linker script defining memory regions, section placements, and boundary symbols
* Manual C runtime initialization:

  * Copies `.data` from Flash to SRAM
  * Zero-initializes `.bss` section
  * Sets up stack pointer and calls `main()`
* Cross-compilation toolchain:

  * Uses `arm-none-eabi-gcc`, `arm-none-eabi-ld`, and `objdump`
  * Generates `.o`, `.elf`, `.bin`, and `.hex` executables
* OpenOCD and GDB integration:

  * Flash programming to target MCU
  * On-chip debugging via SWD/JTAG
* Multi-task LED blinking demo:

  * Implements a simple task scheduler and context switching using PendSV and SysTick exceptions
  * Four LED tasks blink at different frequencies

---

### Project Structure

```
Baremetal_Embedded/
│
├── main.c                # Task scheduler, SysTick, and PendSV handlers
├── main.h                # Task configuration and memory map
├── led.c / led.h         # GPIO driver for LED control
├── stm32_startup.c       # Custom startup code and vector table
├── stm32_ls.ld           # Linker script defining FLASH and SRAM layout
├── syscalls.c            # Minimal system calls for newlib (printf support)
├── Makefile              # Automates compile → link → flash pipeline
├── final.map             # Linker memory map analysis
└── README.md             # Project documentation
```

---

### Build Process Summary

1. **Compile source files**

   ```bash
   arm-none-eabi-gcc -c main.c led.c stm32_startup.c syscalls.c -mcpu=cortex-m4 -mthumb
   ```
2. **Link all objects using custom linker script**

   ```bash
   arm-none-eabi-ld -T stm32_ls.ld *.o -o final.elf
   ```
3. **Convert ELF to binary for flashing**

   ```bash
   arm-none-eabi-objcopy -O binary final.elf final.bin
   ```
4. **Flash and Debug via OpenOCD + GDB**

   ```bash
   openocd -f board/stm32f4discovery.cfg
   arm-none-eabi-gdb final.elf
   (gdb) target remote :3333
   (gdb) monitor reset init
   (gdb) load
   (gdb) continue
   ```

---

### Technical Highlights

| Component          | Description                                                                                           |
| ------------------ | ----------------------------------------------------------------------------------------------------- |
| Processor          | ARM Cortex-M4 (STM32F407)                                                                             |
| Language           | C (Baremetal, no OS)                                                                                  |
| Toolchain          | GNU ARM Embedded (`arm-none-eabi-gcc`)                                                                |
| Flashing/Debugging | OpenOCD + GDB                                                                                         |
| Core Concepts      | Startup code, vector table, linker scripts, C runtime, interrupts, PendSV, SysTick, context switching |

---

### Learning Outcomes

* Understand how embedded programs boot up without an RTOS
* Learn linker scripts, startup code, and section management
* Gain practical knowledge of ARM Cortex-M exception handling
* Develop confidence in command-line embedded workflows
* Experience low-level debugging using OpenOCD and GDB

---

### Conclusion

This project provides a complete hands-on experience in bare-metal embedded development — from startup to flashing — equipping you with an industry-level understanding of how embedded systems boot and run at the lowest level.
