
# Embedded Systems Programming on ARM Cortex-M3/M4 Processor

This repo contains notes and programming assignments for the Udemy's "[Embedded Systems Programming on ARM Cortex-M3/M4 Processor](https://www.udemy.com/course/embedded-system-programming-on-arm-cortex-m3m4/)" course by FastBit Embedded Brain Academy.

Date: July, 2023.

- The course is instructed by Engineer Kiran Nayak.

- The [**Certificate**](https://github.com/renatosoriano/Udemy-Embedded-Course2_Embedded-Systems-Programming-on-ARM-Cortex-M3-M4-Processor/blob/main/Certificate.pdf) is available. 

## Descriptions

This course is for Embedded Engineers/Students who want to learn and Program ARM Cortex M3/M4 based controllers by digging deep into its internals and programming aspects:

1. Internal architecture of ARM Cortex M3/M4 processor and programming.
2. Learn Mixed ‘C’ and Assembly Coding using inline assembly technique.
3. Demystifying Memory, Bus interfaces, NVIC, Exception handling with lots of animation.
4. Interrupts and configuration of ARM Cortex Mx based microcontroller.
5. Low level register Programming for interrupts, System Exceptions, Setting Priorities, Preemption,etc.
6. Learn writing IRQ handlers, IRQ numbers, NVIC and MCU more.
7. Implementation of task scheduler using PENDSV and SYSTICK feature of the processor.
8. Implementation of context switching.
9. Learn and write linker script and MCU startup file from scratch.
10. Bare metal embedded build process.
11. Processor fault exceptions and fault handler implementation and fault analysis.
12. Stack and AAPCS standard.
13. Learn inline assembly, naked functions and gcc variable and section attributes.

## Requirements

**[STM32 Nucleo-F446RE Development Board](https://www.st.com/en/evaluation-tools/nucleo-f446re.html#overview)** - Board used in this course but any board with Arm Cortex-M0/3/4 core will work, just modifying the target board and configuring with the respective datasheet. \
**[Eclipse-based STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html)** - C/C++ development platform with peripheral configuration, code generation, code compilation, and debug features for STM32 microcontrollers and microprocessors. Works on Windows/Linux/Mac and is free.

## Notes
* #### FPU warning fix
    Right click on the project -> properties -> expand C/C++ build -> Settings -> Tool settings -> MCU settings
  * `Floating-point unit: None`
  * `Floating-point ABI: Software implementation ( -mfloat-abi=soft )`

![FPU_warning.png](https://github.com/renatosoriano/Udemy-Embedded-Course1_Microcontroller-Embedded-C-Programming-Absolute-Beginners/blob/main/Images/FPU_warning.png)

* #### Setting up SWV ITM Data Console

Open *syscalls.c* file and paste following code bellow *Includes*

```c
/////////////////////////////////////////////////////////////////////////////////////////////////////////
//           Implementation of printf like feature using ARM Cortex M3/M4/ ITM functionality
//           This function will not work for ARM Cortex M0/M0+
//           If you are using Cortex M0, then you can use semihosting feature of openOCD
/////////////////////////////////////////////////////////////////////////////////////////////////////////


//Debug Exception and Monitor Control Register base address
#define DEMCR                   *((volatile uint32_t*) 0xE000EDFCU )

/* ITM register addresses */
#define ITM_STIMULUS_PORT0   	*((volatile uint32_t*) 0xE0000000 )
#define ITM_TRACE_EN          	*((volatile uint32_t*) 0xE0000E00 )

void ITM_SendChar(uint8_t ch)
{

	//Enable TRCENA
	DEMCR |= ( 1 << 24);

	//enable stimulus port 0
	ITM_TRACE_EN |= ( 1 << 0);

	// read FIFO status in bit [0]:
	while(!(ITM_STIMULUS_PORT0 & 1));

	//Write to ITM stimulus port0
	ITM_STIMULUS_PORT0 = ch;
}
```


After that find function *_write* and replace `__io_putchar(*ptr++)` with `ITM_SendChar(*ptr++)` like in code snippet below
```c
__attribute__((weak)) int _write(int file, char *ptr, int len)
{
	int DataIdx;

	for (DataIdx = 0; DataIdx < len; DataIdx++)
	{
		//__io_putchar(*ptr++);
		ITM_SendChar(*ptr++);
	}
	return len;
}
```

After these steps navigate to Debug configuration and check `Serial Wire Viewer (SWV)` check box like on snapshot below

![Debugger.png](https://github.com/renatosoriano/Udemy-Embedded-Course1_Microcontroller-Embedded-C-Programming-Absolute-Beginners/blob/main/Images/Debugger.png)

Once you enter *Debug* mode, go to `Window -> Show View -> SWV -> Select SWV ITM Data Console`. On this way `ITM Data Console` will be shown in *Debug* session.


In `SWV ITM Data Console Settings` in section `ITM Stimulus Ports` enable port 0, so that you can see `printf` data.



