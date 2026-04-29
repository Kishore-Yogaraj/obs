The first thing you need to know is that the the STM32 is not the entire board it is just the big square chip that is in the middle of the board.

![[Pasted image 20250929123813.png]]

This big chip is also not just the CPU, it is a **microcontroller**. A microcontroller is a single chip that contains the:
- CPU core (ARM Cortex-M33)
- Memory (Flash for program storage, SRAM for working memory)
- Peripherals (timers, GPIO, SPI, I2C, UART, ADC, etc.)

All the extra things around the board such as pins and holes for the pins and LEDS are so that you can work with the board immediately without needed to make your own PCB. With the STM32 chip by itself you would need your own PCB with power, crystal oscillator, programming header, etc. The board above gives you all of that for free so you can immediately start interfacing with the outside world using the provided pins and holes to write programs.

### CPU Specs

**32-bit Core**
This means that the registers on the CPU are 32 bits wide so each register can hold numbers up to 2^32 - 1 which means numbers from 0 to about 4.3 billion or a signed integer in that range.

You can think of registers as the CPU's "scratch pad". The CPU can not do arithmetic directly in memory (RAM). Every math operation first loads the values into registers. 

![[Pasted image 20250929125611.png]]

Memory can be considered the long term workspace. When you declare variables in C++:

![[Pasted image 20250929125703.png]]

The compiler first allocates space in RAM for a, b and c. The CPU can't just do math in RAM directly so it:
- Loads them into registers
- Performs the operations
- Stores the result back into RAM (if needed)

On the STM, we only have 13 general purpose registers from (R0 to R12). If we use a lot of variables at once the compiler can't fit them all into registers so the extra on gets "spilled" into RAM which is a lot slower. This is why embedded programmers think carefully about memory usage and code efficiency. 

**RAM (Up to 786KB)**
- RAM is your volatile working memory and it is erased when power is lost
- Its used for variables, function calls and local variables, buffers and its much faster to access than Flash but smaller and temporary

![[Pasted image 20250929130332.png]]

RAM also has "cells" but they are of smaller size than the CPU core. 

Registers:
- Small number (13 general purpose)
- Each register is 32 bits wide
- They live inside the CPU core

RAM:
- Much larger (has up to 786KB)
- Made up of bytes (8 bit cells) each with a unique address
- Live outside the CPU core and is connected via the bus

We can think of RAM as a giant one-dimensional array of bytes:
![[Pasted image 20250929131254.png]]

Each cell holds 1 byte (8 bits). The CPU can read and write a byte, halfword, or word but the smallest unit of addressable memory is always a byte.

Let's say we declare that int x = 1234

int on STM32 = 32 bits which is 4 bytes. The compiler would pick an address in RAM (0x20000000). The 4 bytes from 0x20000000 to 0x20000003 would be reserved for x. If we looked at memory we would see those 4 bytes holding the binary form of 1234.

#### Understanding moment
With the above example I learned that RAM has individual cells that store 8 bits which is also 1 byte. This means that it can store numbers from 0 - 255 in each "cell". Let's say we had a number greater than 255, it would then require more than 1 byte so it would allocate two cells to be used to store the number. This means that two different addresses would be reserved to show this number.

Similarly this is how it works with 32 bit registers in the CPU core. A 32 bit register can hold a number from 0 to 4.3 billion. Let's say you have a number that is is greater than 32 bits, then the CPU would allocate two registers for that number.

##### 64 bit numbers in RAM
On the STM32U575 (Cortex-M33, a 32 bit CPU):
- 1 register = 32 bits (4 bytes)
- A 64 bit integer (long long in C++) = 8 bytes
In RAM the compiler reserves 8 consecutive byte addresses for that number
- Example:
![[Pasted image 20250929134720.png]]
All the above addresses are allocated to this 64 bit number. Multiple cells (bytes) in RAM form one larger number

##### 64 bit numbers in registers
Since each register is only 32 bits:
- A 64 bit number would take two registers
- R0 = low 32 bits, R2 = high 32 bits

**I wanted to understand what would happen if you took a look at one of the memory addresses of a 16 bit integer but I think that might be slightly out of my scope**

**Flash**
- Flash means non volatile memory so contents stay even when power is off
- On the STM32, your compiled program (machine instructions are stored here)
- When you flash code, you are literally writing the binary instructions into the chip's Flash

### Flash, Registers, RAM

The next thing we should understand is how we write our program, how it takes up space in memory and how the CPU uses it. This is important for writing effective code on a microcontroller
#### Writing Your Program 
Let's say you write something like this:
![[Pasted image 20250929153024.png]]

At this stage:
- This is just text that you understand and is not yet usable by the STM32

#### Compilation and Linking
The compiler (which is an arm-none-eabi-g++):
- Translates the C++ into machine code instructions for the STM32U575's Cortex-M33 CPU
- Allocates memory:
	- Decides which variables should go into Flash (constants, program code)
	- Decides which variables go into RAM (global variables, stack)
- Linker:
	- Arranges all pieces together into one binary file (.elf, .bin)

The result of all this is a binary image which a file full of raw instructions and initial data

#### Flashing the Program
- The ST-LINK debugger/programmer on the nucleo board will take the binary file we complied and writes it into the STM32's internal flash memory
- When you reset or power cycle the board, the CPU starts executing from address 0 x 08000000 (the base of the flash)
So now the C++ program we wrote physically lives as binary in Flash

#### Reset and Startup
On reset:
- Boot ROM (tiny code inside STM32) sets up the chip
- CPU reads the vector table (at start of Flash) to know where main() and interrupts are
- The startup code copies initial data into RAM:
	- For example: int x = 5, the value 5 stored in flash gets copied into RAM at run time
- Then we jump into the main function

#### Execution Flow

Now the CPU runs instructions in a loop

Instructions come from Flash
- Flash contains the binary instructions compiled from the C++ code we wrote
- Example: "Load a from memory" or "Add two registers"

Variables live in RAM
- When you declare int a = 100; the compiler reserves 4 bytes in RAM 
- The CPU stores and retrieves the value of a from there

Arithmetic happens in Registers
- The CPU cannot add directly in RAM
- Instead
	- Load value of a from RAM into register R0
	- Load value of b into another register R1
	- Perform ADD R2, R0, R1
	- Store result (c) back into RAM 

#### The Big Picture Analogy
**Flash** = Book shelf (your program written down, permanent)
**RAM** = Desk (Workspace for variables; cleared when you leave)
**Registers** = Hands (the CPU can only do math with what it's hold)
**CPU** = You (reads instructions from the book, grabs data from desk, uses hands to manipulate, writes back to desk)

#### Flash Memory
Flash holds things that must survive resets and power cycles:
- Program instructions (machine code compiled from your C++)
- Constants/literals `const int table[4] = {1,2,3,4}`
- Initial values for global/static variables (the startup code copies them into RAM)
- Interrupt vector table (addresses for reset and interrupts)

#### RAM
RAM holds everything that changes while the program runs:
- Global/static variables (writable) - copied from Flash at startup if initialized
- Heap - for dynamically allocated memory
- Stack - function call frames, local variables, return addresses
- Buffers, temporary arrays
RAM is the working memory and is cleared on rest

#### Registers hold the current operands for the CPU:
- R0 - R12 (general purpose): scratch space for calculations, local variables, function arguments and return values

#### Example Walk Through
```c
int g = 5; //global variable

int main(){
	int a = 100; //local
	int b = 200; //local
	int c = a + b;
	return c;
}
```

**Flash**:
- Machine instructions for main (binary encoding of MOV, ADD, etc.)
- Constant 5 (initial global value g)

**RAM:**
- Global variable g stored at some address in RAM (value initialized to 5 by startup code)
- Local variables a, b, c pushed onto stack when main() runs

**Registers (example during execution):**
- Move 100 to R0
- Move 200 to R1
- Add R0 and R1 and move into R2
- Write R2 back into RAM at the location allocated for c

#### Global Variables
The initial values are stored in Flash and the moved to RAM during execution 
#### Global Const Variables
These values are stored in Flash and may never touch RAM. The CPU pulls them straight from the Flash memory
#### Local Variables
The compiler knows it needs space for local but they don't get initial values stored in Flash. At run time the locals live inside the RAM. When main() runs, space is reserved RAM for the local variables. The compiler created instructions that say to allocate some space at run time for the local variables