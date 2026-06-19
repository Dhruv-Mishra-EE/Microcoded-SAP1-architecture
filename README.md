# Microcoded-SAP1-architecture
8-bit SAP-1 CPU implemented in Logisim Evolution from first principles — complete with microprogrammed controller, SRAM, ALU, and Fibonacci demonstration program. Built with reference to Malvino's Digital Computer Electronics.

# INTRODUCTION
A Logisim implementation of the SAP-1 (Simple-As-Possible Computer 1), a classic educational 8-bit computer architecture. The project demonstrates the core building blocks of a processor, including registers, memory, an ALU, a program counter, control logic, and a common bus system. It provides a practical introduction to digital design, computer organization, and the fetch-decode-execute cycle that underlies modern CPUs.

SAP-1 (Simple-As-Possible Computer 1) is an educational 8-bit computer architecture introduced by Albert Paul Malvino to demonstrate the fundamental principles of digital computer design. Despite its simplicity, SAP-1 incorporates the essential components of a modern CPU, including registers, an arithmetic logic unit (ALU), a program counter, memory, a control unit, and a shared data bus.

This project implements the SAP-1 architecture in Logisim, providing a hands-on demonstration of the fetch-decode-execute cycle, instruction execution, data transfer between registers, and basic arithmetic operations. The design serves as a foundation for understanding computer architecture, digital logic design, and processor development. 

The processor follows the classic SAP-1 architecture and executes instructions through a sequence of micro-operations controlled by the control unit.
Features--
8-bit data bus
Program Counter (PC)
Memory Address Register (MAR)
Instruction Register (IR)
Accumulator (A Register)
B Register
16x8 SRAM module
8-bit Adder/Subtractor ALU
Control Unit with micro-operations
Fetch-Decode-Execute cycle implementation

How to Run
Open computer_8bits.circ in Logisim Evolution.
Load or enter a program into memory.
Reset the processor.
Enable the clock and observe instruction execution.

Future Improvements
Additional instructions
Flags register
Conditional branching
Expanded memory
More advanced ALU operations

ABOUT THIS - It took me a month of focused learning of computer architecture from countless sources and books to build this model from scratch . It was my first time learning LOGISIM EV as well so yes i did devote a lot of time and understanding to this . Some major sources i'd like to credit are Ben Eater's breadboard model , Digital computer electronics by Albert Malvino , Digital logic and computer design by Morris Mano , COA by Morris Mano , Charles Petzold - Code. I had a great time learning from these textbooks.
Any suggestions for improvements are welcome .

Dhruv Mishra
Bachelor's in Electrical Engineering 
NIT Silchar , 1st year 
Thank you 

# SIMPLE AS POSSIBLE ARCHITECTURES
SAP-1 — S1MPLE AS POSSIBLE PROGRAMMABLE COMPUTERS
Purpose: Prove the fetch-decode-execute cycle works. Nothing more.

8-bit data bus
4-bit Program Counter → only 16 memory addresses
16 bytes of RAM total
5 instructions only: LDA, ADD, SUB, OUT, HLT
No flags (no zero flag, no carry flag)
No stack, no branching, no jumps
Controller-sequencer is hardwired combinational logic
One general purpose register (Accumulator A) + B register as ALU input buffer
Limitation: You literally write a loop. No conditional logic possible in this

SAP-2 — A real working computer
Purpose: Add programmability and practical instruction set.
Key additions over SAP-1:

Flags register — Zero flag, Carry flag. Now conditional logic is possible.
Jump instructions — JMP, JZ (jump if zero), JC (jump if carry). This enables loops and branching — Fibonacci becomes trivial here.
Expanded instruction set — MOV, MVI, ADD, SUB, INR, DCR, and more (closer to Intel 8080 subset)
More registers — B and C registers added as general purpose
16-bit address bus → 64KB addressable memory (though not always fully implemented)
Stack pointer — PUSH/POP support, enabling subroutines
Controller-sequencer still hardwired but significantly more complex

Key architectural leap: SAP-2 is Turing complete in practice. SAP-1 is not, because it has no branching.

SAP-3 — Essentially Intel 8085 compatible..i might be working over this soon 
Purpose: Bridge to real microprocessor architecture.
Key additions over SAP-2:

Full 8085 instruction set compatibility — all registers, all addressing modes
Interrupt handling — external events can pause execution
More addressing modes — immediate, direct, indirect, register addressing all supported
16-bit operations — register pairs (BC, DE, HL) for 16-bit data handling
I/O ports — proper IN/OUT port addressing, not just a single output register
More ALU operations — AND, OR, XOR, rotate, complement

# ARCHITECTURE AND ORGANISATION OVERVIEW
ARCHITECTURE OVERVIEW
1)PROGRAM COUNTER (PC_CHIP module)
These are made from 74LS163A chips which are 4 bit seq counters with parallel load. These synchronous, presettable counters feature an internal carry look-ahead for application in high-speed counting designs. The '160, '162, 'LS160A, 'LS162A, and 'S162 are decade counters and the '161, '163, 'LS161A, 'LS163A, and 'S163 are 4-bit binary counters. Synchronous operation is provided by having all flip-flops clocked simultaneously so that the outputs change coincident with each other when so instructed by the count-enable inputs and internal gating. This mode of operation eliminates the output counting spikes that are normally associated with asynchronous (ripple clock) counters; however, counting spikes may occur on the RCO (ripple carry output). A buffered clock input triggers the four flip-flops on the rising edge of the clock input waveform. [Texas Instruments datasheet]

2)MEMORY ADDRESS REGISTER/MAR module
These are 74LS173 chips that function as a buffer register . Pins 1 and 2 are grounded so to convert tri state buffer input to 2 state input These act as interface between CPU and RAM memory and are treated as part of memory unit. MAR only takes the lower 4 bits of the bus — not all 8 bits. The upper nibble is sent to controller sequencer

3)2:1 mux
In practical model , we have a 74LS157 chip to act as 2-1MUX but in Logisim , we can use an inbuilt mux for functioning and simplicity.
Although we can go on and build a MUX using and and not GATES

4)RAM (SRAM_CHIP/SRAM_CIRCUITARY/RAM_ROW/BC)
Well that module looks messy and I'm sorry about it, Logisim didn't allow me to make smaller circuit boards and all the hardwired control made it look like a real back of a household switchboard or even worse.
Anyways what we have is a binary counter , 8 of those make a ram row , 16 of which make a working SRAM along with a decoder.
Practically we use cascaded 74LS189 chips , we'll what I've made is the inside circuitary of the chip (including decoder and ram rows) SO
This module implements the 16×8 Static RAM used in the SAP-1 CPU. It stores both the program instructions and data in a single flat address space — consistent with the Von Neumann architecture that SAP-1 is based on. The memory is built entirely from discrete logic subcircuits rather than Logisim's built-in RAM component, to reflect the actual hardware construction described in Malvino's Digital Computer Electronics and digital design by Mano.
Specifications -
Address width 4 bits (A0–A3)
Data width 8 bits (D0–D7)
Total capacity 16 bytes (16 addressable locations)
Cell type RS flip-flop (1-bit storage cell)
Total storage cells16 × 8 = 128 RS flip-flops in total
ddress Decoding
The 4-bit address (A0–A3) feeds into a decoder_4bits subcircuit, which asserts exactly one of 16 select lines — one per RAM row. Only the selected row responds to read/write operations.
Taking bit 0 from all 16 rows and OR-ing them to get Q0. Same for Q1 through Q7.
This works in Mano's architecture because only one row is selected at a time via the address decoder. So at any given moment, 15 rows output 0 and one row outputs its actual data.This only works because unselected rows output guaranteed 0, not floating. If any unselected row could output 1 accidentally, your data would be corrupted.

Writing
When WE is asserted, the selected RAM row's 8 BC cells latch the values present on D0–D7. Each BC cell contains an RS flip-flop. The data input drives the S (Set) or R (Reset) line of the flip-flop depending on the bit value, storing the bit until explicitly overwritten.

Reading
When the row is selected and WE is low, the stored Q outputs of the 8 BC cells are driven onto the output lines. In the full SAP-1 integration, this output connects to the shared 8-bit bus via a tri-state enable controlled by the CE (Chip Enable) control signal from the controller-sequencer.
Integration in SAP-1

During the fetch cycle (T3):


MAR holds the instruction address
CE goes high (controller-sequencer)
Selected RAM row drives its contents onto the bus
IR latches the instruction byte


During execute cycle (T5) for LDA/ADD/SUB:


MAR holds the data address (from IR lower nibble)
CE goes high again
Selected RAM row drives data onto bus
B register latches the value

5)INSTRUCTION REGISTER(IR_CHIP)-
The IR latches the instruction fetched from RAM and splits it into two parts — the opcode for the controller-sequencer and the address operand for the bus.On the rising clock edge when EN is high, IR latches the full 8-bit instruction from the bus. A splitter then breaks the output:The OPCODE output is permanently active — the controller-sequencer needs it continuously from T3 onwards to generate correct control signals.
D_OUT is tri-state buffered so the IR only drives the bus at T4 when the address operand needs to be sent to MAR.
Any module that puts data onto the shared bus needs a tri-state. Modules that only receive from the bus do not so I've added a control buffer on the lower nibble .
6)ACCUMULATOR,B-REGISTER AND OUTPUT REGISTER
Well again you can use circuitary of 74LS173 register chips or make those on Logisim...which is what I've done
 A Register(accumulator)
The Accumulator is the primary working register of the SAP-1 processor. It stores operands and receives the results of arithmetic and logic operations performed by the ALU. Most instructions operate directly on the contents of the Accumulator, making it the central register for data processing.

 B Register
The B Register serves as a temporary storage location for the second operand required by the ALU. During arithmetic operations, the contents of the B Register are combined with the contents of the Accumulator. Unlike the Accumulator, the B Register is not directly accessible to the output unit and is used primarily as an intermediate register during computation.

 Output Register
The Output Register stores the final result produced by the processor and presents it to the output display. Separating the output from the Accumulator ensures that computed results remain visible even when the Accumulator is subsequently modified by later instructions.
Basically all three are registers with different purposes
6)RING COUNTER
A circular shaft register with only one flipflop being active at a time . The single bit is shifted to the adjacent register at every clock pulse . We could've also made it usng a n bit counter with n-2^n decoder but here I've used JK flipflops to make these in Logisim . You can use 74LS107 chips for the flipflops which are readily available as inbuilt modules in Logisim.

7)Instruction Decoder circuit
The instruction decoder is simply a 4-to-16 decoder circuit . It Takes the 4-bit opcode from IR upper nibble and asserts exactly one output line high.
Input: 0000 → output line 0 high (LDA)
Input: 0001 → output line 1 high (ADD)
Input: 0010 → output line 2 high (SUB)
Input: 1110 → output line 14 high (OUT)
Input: 1111 → output line 15 high (HLT)
So an instruction decoder has dual operation of--
Decodes the opcode bits from the Instruction Register (IR).
Determines which instruction is being executed (LDA, ADD, SUB, OUT, HLT, etc.)

8)MICROPROGRAMMING THE COMPUTER
Well as per Malvino's organisation we should have a control matrix.The control matrix is the decision-making section of the control unit. It receives timing signals from the sequence counter and instruction information from the instruction register. Based on the current instruction and timing state, it generates the appropriate control signals required to perform micro-operations within the CPU.**

During each clock cycle, a specific timing state activates a row of the control matrix. The decoded instruction activates a corresponding column. The intersection of the active row and column determines which control signals are asserted. These signals enable data transfers between registers, memory access operations, ALU functions, and program counter updates.

This is literally just AND gates and OR gates built to form a matrix
BUT AS HARDWIRED CONTROL BECOMES MORE COMPLEX WHAT WE PREFER IS A USING A ROM INSTEAD
Instead of implementing all those AND-OR gates manually, you precompute every combination and store it, which is basically the reason PROMs are built for .
step 1 :Lookup table
step 2: Address = {opcode + T-state} give stored control word output via a counter
I've explained this in a different module

# MICROPROGAMMED CONTROL 
Okay so the first point of difference is that in a hardwired control , we send 7 input bits consisting of opcode + T-state to the control matrix which fetches the microinstruction 
However in microprogrammed control , we send opcode to address ROM and then this counter acts as link between control rom and address rom by using T-state control . Most modern microprocessors use microprogrammed control.

ADDRESS ROM and presettable counter
The inputs are upper nibble say I7I6I5I4 to this ROM ,the value of this nibble fetches the required microinstruction . If it 0000 , it goes to LDA ans so on 
To get control of T states we use a presettable counter. Now it counts normally 
T1-counter outputs 0000
T2-counter outputs 0001
T3-counter outputs 0010
Now the next setp should be 0011. Now here's the thing — 0011 happens to be where LDA starts. So if you're executing LDA, you actually WANT to go to 0011. No loading needed, just let it count.
But what if you're executing ADD? ADD starts at 0110. If the counter just increments from 0010 it hits 0011 — which is LDA's microprogram. That's wrong. 
Loading here means addressing the control ROM so counter has a Load input at T3 and differentiated input at T1 of next cycle so we get a spike which restarts counter over again.Actually,
Loading means the counter jumps to the correct starting address output by the Address ROM. The counter then addresses the Control ROM from that point. The load and the addressing are two separate steps

CONTROL ROM
A ROM that stores the actual microinstructions — the control words that activate specific signals in the CPU at each step.
In SAP-1, Malvino uses a 16 x 12 Control ROM:
16 addresses (4-bit address from the counter)
12 bits wide (one bit per control signal)
 
Regarding Logisim screenshots
programming it meant :You told the Address ROM what to output for each opcode.
When the CPU fetches an instruction and the opcode lands in IR, the upper nibble I7I6I5I4 drives this ROM's address pins. The ROM then outputs the stored value at that address.So when I typed 3 at position 0, I was basically saying:
"When LDA is fetched, tell the presettable counter to jump to address 3 in the Control ROM — that's where LDA's microinstructions live."

As for the counter ,This CTR4 in Logisim is a TTL library component — it's more complex than needed and the pin naming is non-obvious. But since it's inbuilt I decided to use that so here's a pin context (via datasheets)
R        → Reset/CLR → connect your T1 spike here
M1[load] → LOAD → connect T3 here
M2[count]→ Count enable → tie HIGH (always counting)
M3[up]   → Count up → tie HIGH
M4[down] → Count down → tie LOW (don't need this)
G5       → Clock enable → tie HIGH
2,3,5+/C6→ CLK input → connect system clock here
2,4,5-   → inverted CLK → leave unconnected

1,6D (×4)→ Data inputs D0-D3 → connect Address ROM output bits here
Q0-Q3    → outputs → connect to Control ROM address input

Now for the control ROM 
if ROM is addressed to loction 0 then it fetches 5E3H ...what does that mean
5E3 in hex = 0101 1110 0011 in binary (12 bits)
Now map each bit to a control signal using Malvino's key at the bottom of the table
that tell which signals should be ON now. S1mple as that...well un related but S1MPLE is actually my gamer tag. 
Moreover this module eliminates the need of controller sequencer which is much harder to build in hardwired control.

# PROCESSOR INTEGRATION 
PROCESSOR INTEGRATION
1) Make the 8 bit bus. In Logisim we don't have this component so what i did was make use of IR_chip and extend its output cables. YOU can use 4 breadboard sticks , each 2 bits wide if making the cpu practically. 

2) Then add the instruction register using tri state buffers. I have BUS_IN and BUS_OUT both connecting to the same bus. That's correct — the PC can receive data from the bus (for jump instructions) and drive data onto the bus.
But SAP-1 doesn't have a jump instruction. So BUS_IN on the PC will never actually be used in basic SAP-1. 

3)Adding the MAR. MAR output goes to SRAM address pins, not the bus.
MAR is a one-way module — it receives from the bus, but its output never drives the bus back. It only drives the SRAM address input.
SO its enable is set to high always.

4)Add other modules one by one as per main SAP-1 architecture . Add SRAM , IR , ROMs , accumulators , register B and output register. 

5)Next i added  some splitters , tri state buffers and output buses in individual modules and started to actually connect the modules to each other. 

6)MCPM_CONTROLS - AS PER CONTROL UNIT LOOKUP TABLE
OUT_ROM (12-bit) → Splitter (fan out 12) →
    bit 0  → Cp  → PC_chip EN
    bit 1  → Ep  → PC_chip EA (tristate enable)
    bit 2  → Lm  → MAR EN
    bit 3  → CE  → SRAM SEL
    bit 4  → Li  → IR_CHIP EN
    bit 5  → Ei  → IR_CHIP EA (tristate enable)
    bit 6  → La  → accumulator LOAD
    bit 7  → EA  → accumulator tristate enable
    bit 8  → Su  → ALU CTR
    bit 9  → Eu  → ALU En
    bit 10 → Lb  → REG_B LOAD
    bit 11 → Lo  → LATCH Enabled

7) Adding the modules to bus 
Drives the bus (outputs):
PC_chip  OUT_BUS  → bus
SRAM     RAM_OUT  → bus
IR_CHIP  Q_OUT    → bus
accumulator Q_OUT → bus
ALU      ALU_out  → bus

Reads from bus (inputs):
PC_chip  IN_BUS  → bus
MAR      D_IN    → bus
SRAM     DATA_IN → bus
IR_CHIP  D_IN    → bus
accumulator D_IN → bus
REG_B    D_IN    → bus
Now the thing is I've modelled the circuit so that outputs come out as bundles of bits and bytes instead of individual bits. One 8-bit wire connects everything instead of 8 individual wires per module. Clean and manageable circuit :-) 
SAP-1 is a Von Neumann architecture because instructions and data share the same memory, that means you need not to separate data and address buses in your Logisim implementation.

8) One thing i'll like to tell you is that I've added bit extenders (4-8) like we can extend the sign of a number 
Example- if 1001 is to be extended then we can write it as 1111 1001 as signed extension . This module is available in Logisim . This is done to tackle the incompatible bit width problem between output cables and bus.

9)In program counter chip , the output is connected to tri state buffer and a bit extender . In MAR , since bus is used to input we have a splitter , lower nibble goes to the BUS. The d_out of MAR goes to add_in of SRAM. As in textbook , data input can be left floating as it is pre progammed before computer run.

10)Made the clock , reset and connect to all modules. MCPM control connects to T1,T3 of ring counter and basically.... we're almost done.One more thing ,When the Accumulator is not outputting to the bus ($E_A$ is disabled), its Q_OUT pins will go into a high-impedance floating state (High-Z). Because the ALU is hooked to this exact line, the ALU inputs will float (become undefined). The ALU needs to constantly "see" the Accumulator and B register values regardless of what is happening on the bus.so we Give the Accumulator subcircuit two separate outputs: one dedicated, direct 8-bit path straight to the ALU's A_in (no tri-state), and a separate 8-bit path that goes through a tri-state buffer to the W-bus.

11) The ring counter triggers on falling edge of the clock. When the clock drops from high to low, the counter increments. This gives the ROM half a clock cycle of "dead time" to avoid static time violations .I have not conneceded the clock line to these modules: 
The RAM in a basic SAP-1 is asynchronous. It outputs data purely based on the address coming from the MAR and whether the RAM enable signal is active: 
The ALU is made entirely of combinational logic gates. It calculates instantly whenever the Accumulator or B Register changes. It has no internal memory and no clock 
The ROM itself doesn't take a clock; it just decodes whatever address is handed to it by the counter and the Instruction Register 

12) Then we're done.

# INSTRUCTION SET AND CONTROL LOOK UP TABLE
INSTRUCTION SET
Opcode	Mnemonic	Operation
0000	LDA	   Load accumulator from memory
0001	ADD	   Add memory data to accumulator
0010	SUB	   Subtract memory data from accumulator
1110	OUT	   Transfer accumulator contents to output register
1111	HLT	   Halt execution

ROM MICROCODES 
T-State	LDA	ADD	SUB	OUT	HLT
T1	CO, MI	CO, MI	CO, MI	CO, MI	CO, MI
T2	RO, II, CE	RO, II, CE	RO, II, CE	RO, II, CE	RO, II, CE
T3	IO, MI	IO, MI	IO, MI	AO, OI	HLT
T4	RO, AI	RO, BI	RO, BI	—	—
T5	—	EO, AI	EO, AI, SU	—	—
T6	—	—	—	—	—

Control Signal Meanings
Signal	Description
CO	Program Counter Out
CE	Program Counter Enable (increment)
MI	Memory Address Register In
RO	RAM Out
II	Instruction Register In
IO	Instruction Register Address Out
AI	Accumulator In
AO	Accumulator Out
BI	B Register In
EO	ALU Out
SU	ALU Subtract Mode
OI	Output Register In
HLT	Halt Clock

EXAMPLE - SAP_CODE01 AND 02 (GIVEN BELOW)

# PROGRAM DEMONSTRATION 1 - ADDITION/SUBTRACTION 
Following is step by step process to demonstrate how to run a basic addition program on SAP-1 architecture . the instruction cycles are also mentioned in images.
Problem: 16+20+24-32

Instruction by instruction
Address 0H — LDA 9H

Load contents of address 9H into accumulator (register A)
A ← 16
Machine code: 0000 1001 → upper nibble 0000 = LDA opcode, lower nibble 1001 = address 9H

Address 1H — ADD AH

A ← A + contents(AH) = 16 + 20 = 36
Machine code: 0001 1010 → 0001 = ADD opcode, 1010 = address AH

Address 2H — ADD BH

A ← 36 + 24 = 60
Machine code: 0001 1011

Address 3H — SUB CH

A ← 60 - 32 = 28
Machine code: 0010 1100 → 0010 = SUB opcode

Address 4H — OUT

Transfer accumulator value to output register → displays 28
Machine code: 1110 XXXX → lower nibble irrelevant, SAP-1 ignores it

Address 5H — HLT

Stop the clock, execution halts
Machine code: 1111 XXXX

Addresses 6H–8H — XX (unused)
Just empty memory, not part of the program 

9H-CH store the data as mentioned earlier 
The program (instructions) occupies the lower addresses, and the data occupies the higher addresses. Instructions come first — that's what "stored ahead" means. Ahead = lower addresses in memory.

# PROGRAM DEMONSTRATION 2 - FIBONACCI SEQUENCE 
SAP_CODE02
FIBONACCI SEQUENCE PROGRAM demonstration 
Address 0: LDA 14   → load contents of address 14 into accumulator
Address 1: OUT      → display accumulator
Address 2: ADD 15   → add contents of address 15 to accumulator
Address 3: OUT      → display accumulator
Address 4: HLT      → stop
Address E: 00       → first Fibonacci number
Address F: 01       → second Fibonacci number 

For anyone who's unaware , a Fibonacci sequence is defined as
Tn=Tn-1+Tn-2 , T0=0 and t1=1 
a simple C code would be 

#include <stdio.h>

int main()
{
    int n, a = 0, b = 1, next;

    printf("Enter number of terms: ");
    scanf("%d", &n);
   
    printf("Fibonacci Sequence:\n");

    for(int i = 0; i < n; i++)
    {
        printf("%d ", a);
        next = a + b;
        a = b;
        b = next;
    }

    return 0;
}

Now actually here in code , we take the input from user . Since this is an SAP design , we have to manually load SRAM registers addressed 14 and 15. This is the fundamental reality of early computers — the program lives in memory before execution begins.Before you start the clock in Logisim, you manually enter the program into SRAM via the hex editor. Just like you programmed the ROM contents earlier.You open SRAM hex editor → type in the instructions → close → run clock and you get outputs 

# REFERENCES
Primary Textbooks
Malvino, A.P. & Brown, J. — Digital Computer Electronics, 3rd Edition
Mano, M.M. — Computer System Architecture
Mano, M.M. — Digital Design and computer design
CHARLES P. - CODE :The Hidden Language of Computer Hardware and Software
Johan H. Huijsing - Operational Amplifiers - Theory and Design

Electronics / Analog Reference
Razavi, B. — Microelectronics and design of CMOS analog circuits

Video Lectures
Ben Eater — Building an 8-bit CPU from scratch (YouTube) (breadboard CPU implementation, EEPROM microcode)
NPTEL — Computer Organization and Architecture, Prof. Kamakoti, IIT Madras
MIT OCW Microelectronic Devices and Circuits

EDA Tools Used
Logisim Evolution (digital circuit simulation and construction)

OTHERS
Texas Instrument datasheets (internal circuit understanding)

# CONCLUSIONS AND FUTURE 
Limitations
No flags, no branching, 16 bytes only.
Moreover you may notice that 3E3H state is a nop , one way to speed up SAP 1 operation is to eliminate nop states. This would shorten a machine cycle..like for LDA it would be 5 states and not 6. A variable machine cycle is shown in one of the schematics..though not implemented in my design. When T6(nop) state begins, control ROM  produces output of 3E3H and we use some combinational logic to detect this and send low output signal.

FUTURE PLANS
Rewrite SAP-1 completely in Verilog HDL
Self-checking testbenches for each module
Waveform verification in GTKWave
Extend to SAP-2 in Verilog
Add flags register (Zero, Carry)
Implement conditional branching (JZ, JMP)

