### Introduction

**What is an Operating System?**

An **Operating System (OS)** is system software that:

- Manages and coordinates hardware resources, including one or more processors.
    
- Provides essential services to users and application software.
    
- Handles memory management, input/output operations, and networking functions.
    

---

### Basic Elements of a Computer System

#### Processor
The **processor**, or **Central Processing Unit (CPU)**:

- Controls the overall operation of the computer.
    
- Executes instructions and performs data processing tasks.

#### **Main Memory**
Main memory is used to store data and programs currently in use. It is:

- Typically **volatile**, meaning its contents are lost when the system is powered off.
    
- Also known as **primary memory** or **real memory**.

#### **I/O Modules**
**Input/Output (I/O) modules** facilitate communication between the computer and the external environment by:

- Transferring data to and from peripheral devices such as keyboards, displays, storage, and networks.


#### System Bus
Provides for communication among processors, main memory, and I/O modules. 

---

### Processor Registers

Processor registers are **faster and smaller** than main memory and play a crucial role in instruction execution and performance optimization.

#### **Types of Processor Registers**

1. **User-Visible Registers**  
    These registers can be accessed directly by both application and system programs. They help reduce the need to access slower main memory.
    
    - **Data Registers**: Hold data values and can be modified by user programs.
        
    - **Address Registers**: Store memory addresses used in instruction execution. These include:
        
        - **Index Registers**: Used for indexed addressing.
            
        - **Segment Pointers**: Help in segment-based memory addressing.
            
        - **Stack Pointers**: Manage the runtime stack (both user and supervisor stacks).
            
2. **Control and Status Registers**  
    These may or may not be accessible to the user. They are essential for controlling processor operation and tracking execution state.
    
    - **Program Counter (PC)**: Holds the address of the next instruction to execute.
        
    - **Instruction Register (IR)**: Contains the most recently fetched instruction.
        
    - **Condition Codes (Flags)**: Reflect the outcome of operations (e.g., zero, carry, overflow).
        
    - **Memory Address Register (MAR)** / **Memory Buffer Register (MBR)**: Handle memory access operations.
        
    - **Program Status Word (PSW)**: A special-purpose register containing:
        - Condition codes    
        - Interrupt status
        - Processor mode (user/supervisor)

![[Pasted image 20250514172214.png|center]]


### **Instruction Execution**

A program is composed of a sequence of instructions stored in memory. The processor follows a two-step cycle to execute these instructions:

#### 1. Fetch

- The processor retrieves the next instruction from memory, using the **Program Counter (PC)** to locate it.
    
- After fetching, the PC is incremented to point to the next instruction.
    
- The fetched instruction is stored in the **Instruction Register (IR)**.

### 2. Execute

- The instruction in the IR is decoded and executed by the processor.

---

### **Types of Instructions**

1. **Processor-Memory Instructions**  
    Transfer data between the processor and memory.
    
2. **Processor-I/O Instructions**  
    Transfer data between the processor and I/O devices.
    
3. **Data Processing Instructions**  
    Perform arithmetic or logic operations.
    
4. **Control Instructions**  
    Alter the sequence of execution (e.g., jumps, branches).
    

---

### **Interrupts**

Interrupts are mechanisms that allow external or internal events to **interrupt the normal flow of instruction execution**, improving processor efficiency.

#### Why Interrupts Are Needed

- Many I/O devices operate significantly slower than the processor.
    
- Without interrupts, the processor would waste cycles waiting for devices (polling).
    

> **Example:**
> 
> - A 1 GHz processor executes ~10⁹ instructions per second.
>     
> - A hard disk rotating at 7200 RPM has a half-track time of ~4 ms, which is **4 million times slower** than the processor.
>     
> - Waiting would lead to significant inefficiency.
>     

---
### **Interrupt Handling Process**

#### Interrupt Service Routine (ISR)

Whenever there is an interrupt, control is transferred to this program.
It determines the nature of the interrupt and performs the necessary
actions to handle it.

![[Pasted image 20250514173321.png|center]]

#### Interrupt Cycle

- At the **end of each instruction cycle**, the processor checks for pending interrupts:
    
    - **No interrupt:** Continue with the next instruction.
        
    - **Interrupt detected:**
        - Suspend current program execution, Execute the corresponding ISR, and after ISR, resume the interrupted program.

![[Pasted image 20250514173510.png|center]]


#### Multiple Interrupts

When multiple interrupts occur, the processor must decide how to manage them. Two common approaches are used:

**Solution 1: Disable Interrupts During Interrupt Processing**

- While an interrupt is being handled, **all other interrupts are temporarily disabled**.
    
- Any additional interrupts remain **pending** until the current Interrupt Service Routine (ISR) completes.
    
- After the ISR finishes, the processor **re-enables interrupts** and checks for any pending ones.    

Advantages:

- Simple and easy to implement.
    

Disadvantages:

- Interrupts are handled in **strict sequential order**, regardless of their importance.
    
- **No support for prioritizing** more critical interrupts.
    

**Solution 2: Use a Priority Scheme (Nested Interrupts)**

- Each interrupt is assigned a **priority level**.
    
- **Higher-priority interrupts** can interrupt a currently running ISR for a **lower-priority interrupt** (this is known as **nested interrupt handling**).
    

Advantages:

- Ensures **critical interrupts** are addressed first.
    
- Improves system responsiveness for high-priority events.
    

Disadvantages:

- More complex to implement.
    
- Requires additional hardware or software logic to manage priorities.

---

### Caching

Caching is a key optimization used in hardware, OS, and software to improve access speed.

- **Concept**: Frequently used data is temporarily copied from slower to faster storage (cache).
    
- **Cache Hit**: Data is found in cache → fast access.
    
- **Cache Miss**: Data is not in cache → fetched from slower memory, then cached.
    
- **Caches** are smaller but faster than the storage they mirror.

- **Replacement Policy**: Manages which data to evict (e.g., LRU, FIFO).

- **Multiple Level Cache**: Gets slower and bigger close to Main Memory and faster away from it.

![[Pasted image 20250514174408.png | center]]

--- 

### I/O Structure

When the processor encounters an input/output (I/O) instruction during program execution, it executes that instruction by issuing a command to the appropriate I/O module. 

This interaction can be implemented using one of three main techniques: 
- **Programmed I/O**
- **Interrupt-Driven I/O**
- **Direct Memory Access (DMA)**.

#### Programmed I/O

Upon receiving an I/O request, the processor sends it to the corresponding I/O module. 

The I/O module performs the actual data transfer and updates a status register, indicating whether the operation has completed or not. However, it does not notify the processor when the task is done. 

Instead, the processor must continuously check (*poll*) the status register to determine completion.

This method is simple but inefficient, as it forces the processor to remain occupied with the I/O process, even when it could be executing other tasks.


#### Interrupt Driven I/O

Interrupt-driven I/O offers a more efficient alternative. In this approach, the processor sends a command to the I/O module and then continues executing other instructions. When the I/O module is ready to transfer data, it interrupts the processor to complete the data exchange. This allows the CPU to remain productive while the I/O operation is in progress.

Although this method is more efficient than programmed I/O because it eliminates *busy-waiting*, it still involves the processor in every data transfer. Each byte or word passed between the I/O device and memory must go through the CPU, which can become a bottleneck for large-volume data transfers.

#### Direct Memory Access
 
 In a DMA-based system, the processor delegates the responsibility of data transfer to a dedicated DMA controller. The processor specifies parameters such as whether the operation is a read or write, the target I/O device, the starting memory address, and the number of words to transfer.

Once initiated, the DMA controller manages the entire data transfer directly between the device and memory, with no processor intervention. Only when the complete block of data has been transferred does the DMA controller interrupt the processor to signal completion. 

This approach minimizes CPU involvement, reduces the number of interrupts (one per block rather than one per byte), and greatly improves system efficiency. It is particularly well-suited for handling large data volumes, such as those from disk drives or network cards.

--- 

### Computer System Architecture

