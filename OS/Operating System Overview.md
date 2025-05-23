
An **Operating System (OS)** is a crucial program that manages the execution of application programs and acts as a bridge between the user’s applications and the computer hardware. It ensures that applications can function properly by managing resources like memory, processors, and I/O devices efficiently.

### **Objectives of an Operating System**

The main goals of an operating system are:

- **Convenience**: It simplifies the user’s interaction with the computer, making it easier to use.
    
- **Efficiency**: It ensures that system resources—such as CPU time and memory—are utilized effectively.
    
- **Flexibility and Evolution**: It supports development and testing of new system functions without disrupting ongoing services, allowing the system to evolve over time.
    

### **Key Services Provided by the Operating System**

The OS offers a variety of services to support both system-level and user-level tasks:

- **Program Development**: It provides tools like text editors, debuggers, and software frameworks to aid in writing and testing code.
    
- **Program Execution**: The OS handles the loading of instructions and data into memory, initializes I/O devices and file systems, and manages the scheduling of required resources to execute programs.
    
- **I/O Operations**: It provides a consistent interface for accessing I/O devices, abstracting the hardware complexity from applications.
    
- **File Access and Management**: The OS controls access to files by handling data structures, enforcing permissions, and managing file sharing, protection, and caching.
    
- **System Access and Protection**: It enforces user permissions, ensures secure access to system resources, and resolves conflicts between competing processes.
    
- **Error Detection and Response**: The OS detects hardware failures (like memory or device errors) and software faults (such as illegal memory access or arithmetic errors). It responds appropriately, either by reporting the error or terminating the affected process.
    
- **Accounting and Performance Monitoring**: The system collects performance data and usage statistics to help with system tuning, billing (in multiuser environments), and planning future enhancements.

#### Basic OS Organization

![[Pasted image 20250514233002.png|center]]

---

### **Resource Management by the OS**

The operating system is responsible for managing key system resources, including:

- **I/O devices** (keyboards, displays, disks, etc.)
    
- **Main memory**
    
- **CPU (processor)**

While part of the OS (the kernel) remains in main memory, the rest may reside on secondary storage. User programs and their data also share space in main memory, and it’s the OS—alongside memory management hardware—that controls how this memory is allocated and protected.

### **Modes of Operation**

To ensure **security and stability**, modern operating systems implement **multiple modes of operation**, mainly:

- **User Mode**:  
    In this mode, user applications run with **limited privileges**. Access to certain memory regions is restricted, and some hardware-related instructions are disallowed to prevent unintended interference with the system.
    
- **Kernel Mode**:  
    The OS itself (or **monitor**) runs in kernel mode, which has **full access** to hardware and memory. Here, **privileged instructions** can be executed, and the OS can interact directly with all hardware components.
    

This division protects the system from errors or malicious behavior by user programs, as only the kernel can perform critical tasks.

---

## **Multiprogramming**

In a typical system, the **CPU is much faster** than I/O devices. If the processor had to wait every time a program accessed I/O, it would remain idle frequently. **Multiprogramming** addresses this inefficiency.

With multiprogramming:

- Multiple programs are loaded into main memory simultaneously with a single processor.
    
- The OS selects one program (job) to run.
    
- When that program needs to wait (e.g., for an I/O operation), the OS switches the CPU to another job.
    
- This ensures that the CPU remains active and productive, improving system throughput.

### **Uniprogramming vs. Multiprogramming**

- **Uniprogramming**:  
    The processor executes one job at a time. If the job performs an I/O operation, the CPU **waits** for it to complete before proceeding.
    
- **Multiprogramming**:  
    When a job initiates I/O and becomes blocked, the CPU immediately **switches to another ready job**, maximizing processor utilization.
    
![[Pasted image 20250514234312.png|center]]

### **Time Sharing**

**Time sharing** is an extension of multiprogramming, designed to support multiple **interactive** users simultaneously. It works by dividing the processor's time among users through rapid switching, giving the illusion that each user has their own dedicated machine.

In this model, the CPU allocates a fixed time slice (also known as a **quantum**) to each user process. When the time slice ends, the current process is **preempted**, and the next process in the queue is loaded and executed. This continuous context switching allows several users to interact with the system almost **simultaneously**, making it ideal for **transaction processing** and **interactive computing**.

One of the earliest implementations of this concept was the **Compatible Time Sharing System (CTSS)**, developed in 1962. It supported up to **32 users**, with a system memory of **32K 36-bit words**, and switched between users every **0.2 seconds**, providing a responsive user experience.

Time sharing focuses on **minimizing response time**, rather than maximizing processor throughput, to maintain interactivity and the feeling of **virtual ownership** of the computer by each user.

![[Pasted image 20250514234409.png|center]]

### **Challenges of Time Sharing**

Despite its advantages, time sharing introduces several system design challenges:

- **Memory Protection**:  
    With multiple user programs in memory, the system must ensure that no process can access or modify another’s data.
    
- **File System Security**:  
    Access to files must be restricted to authorized users to maintain privacy and integrity.
    
- **Resource Contention**:  
    Shared resources such as **printers**, **storage devices**, and **network interfaces** must be managed carefully to avoid conflicts and ensure fairness.

--- 

### Brief on Processes

At the core of any operating system lies the concept of a **process**. It is a fundamental unit of execution that allows the OS to manage multiple tasks efficiently. A **process** can be defined in several complementary ways:

- A **program in execution**
    
- An **instance** of a running program
    
- An **entity** that is assigned to a processor for execution
    
- A **unit of activity** characterized by a single sequential thread of execution, a current state, and a set of associated system resources

##### Why Processes Matter

The development of processes was shaped by three major trends in computer systems:

1. **Multiprogramming Batch Systems**  
    The processor switches among multiple programs loaded into memory to maximize resource utilization.
    
2. **Time-Sharing Systems**  
    The system must remain responsive to many users simultaneously, requiring rapid context switching.
    
3. **Real-Time Transaction Processing**  
    Systems that handle numerous concurrent queries and updates to databases, demanding precise timing and coordination.

##### Interrupts: The Core Mechanism

The primary mechanism that enabled multiprogramming and time-sharing was the **interrupt**. When a process is interrupted—such as when waiting for I/O completion—the CPU must:

- **Save the current context** (program counter, CPU registers, etc.)
    
- **Branch to the interrupt handler**, which processes the event
    
- **Resume another process** waiting for execution
    

This mechanism allows efficient multitasking, but it also introduces complexity in **timing** and **coordination**.

##### Challenges in Process Coordination

Managing processes in a multitasking system is not without problems. Some of the key issues include:

- **Improper Synchronization**  
    Poorly designed signaling mechanisms can lead to missed or duplicated signals, causing processes to behave unpredictably.
    
- **Failed Mutual Exclusion**  
    When two processes try to access the same resource (e.g., editing the same file), the OS must ensure that only one succeeds at a time.
    
- **Non-Deterministic Operation**  
    Interleaved execution may lead to one process unintentionally modifying shared memory used by another.
    
- **Deadlocks**  
    A situation where two or more processes are waiting on each other indefinitely, halting system progress.
    

To address these issues, a **systematic coordination method** is essential for reliable process execution.

##### Structure of a Process

A process consists of three key components:

1. **Executable Program**  
    The actual code to be run.
    
2. **Associated Data**  
    Includes variables, buffers, stacks, and other working data.
    
3. **Execution Context (Process State)**  
    This is the internal information the OS needs to manage the process, such as:
    - Contents of the CPU registers
    - Program counter (PC)
    - Process priority
    - Status flags (e.g., waiting on I/O)

The **execution context** is critical because it allows the OS to suspend, resume, and control processes accurately.

---

### Memory Management

The operating system has five primary responsibilities in managing memory:

1. **Process Isolation**  
    Ensures that individual processes are protected from interfering with each other’s instructions and data.
    
2. **Automatic Allocation and Management**  
    The OS handles memory allocation transparently, without requiring user intervention.
    
3. **Support for Modular Programming**  
    Enables dynamic creation and termination of processes and modules, supporting flexible program design.
    
4. **Protection and Access Control**  
    Controls access to shared memory resources, ensuring only authorized access.
    
5. **Long-Term Storage**  
    Provides persistent storage for programs and data, even after power loss.

**Mechanisms to Support These Responsibilities**

- **Virtual Memory**  
    Allows programs to operate as if they have access to a large, contiguous block of memory, abstracted from physical limitations.
    
- **File System**  
    Offers a long-term storage facility using named objects (files) to store and retrieve data efficiently.

##### Paging

Paging is a fundamental technique in virtual memory management:

- A **process** is divided into fixed-size blocks called **pages**.
    
- A **virtual address** consists of:
    - A **page number**
    - An **offset** within that page
        
- The **Memory Management Unit (MMU)** maps virtual addresses to physical addresses dynamically.
    
- **Pages** can be loaded into any location in main memory.
    
- If a page is **not in main memory** when referenced, a **page fault** occurs, and the required page is loaded from disk.

![[Pasted image 20250515165929.png|center]]

##### Key Design Considerations:

- **Efficient Address Translation**: Minimize overhead introduced by the mapping process.
    
- **Optimal Storage Allocation Policy**: Reduce data transfer between memory levels to maintain performance.
    
##### Data Protection and Security

The operating system must enforce security through four key principles:

1. **Availability**  
    Ensure system resources and services are accessible and resistant to interruptions or failures.
    
2. **Confidentiality**  
    Prevent unauthorized access to sensitive information. Users should only access data they are permitted to see.
    
3. **Data Integrity**  
    Protection against unauthorized modification or corruption of data.
    
4. **Authenticity**  
    Verify the identity of users and ensure the validity of messages and data.

---


### Scheduling and Resource Management

The **Operating System (OS)** is responsible for managing and allocating system resources efficiently among active processes. This includes managing:
- **Main memory**
- **CPU**
- **I/O devices**

To ensure effective use of these resources, the OS must implement **scheduling and resource allocation policies** that balance multiple objectives.

**Key Factors in Resource Management**
1. **Fairness**  
    Ensure equal and unbiased access to resources among all processes.
    
2. **Differential Responsiveness**  
    Prioritize certain classes of jobs (e.g., interactive vs. batch) based on their requirements.
    
3. **Efficiency**  
    Maximize system throughput, minimize response time, and support the largest number of users or tasks possible—often involving trade-offs.

##### Types of Scheduling Queues Maintained by the OS

1. **Short-Term Queue (Ready Queue)**
    - Contains processes that are in (or partially in) **main memory** and ready to run.
        
    - Managed by the **short-term scheduler**, which selects the next process to execute using algorithms like **Round Robin**, **Priority Scheduling**, etc.
        
2. **Long-Term Queue (Job Queue)**
    - Holds **newly submitted jobs** waiting to be admitted into the system.
        
    - The OS allocates memory and moves jobs to the short-term queue based on resource availability.
        
    - Prevents **over-committing** CPU or memory.
        
3. **I/O Queues**
    - Each **I/O device** has its own queue of processes waiting to perform I/O operations.
    - The OS decides **which process** gets access to an available I/O device based on scheduling policy.

**System Calls and OS Entry Points**
- A **process requests OS services** (like I/O, memory access, or file operations) through a **system call**.
    
- A system call is a defined **entry point into the operating system**, allowing user-level processes to safely request privileged operations.

![[Pasted image 20250515171431.png|center]]

---
### System Structure

Layered architecture organizes the operating system as a series of levels, where:

- **Each level performs a specific set of functions**.
    
- **Higher levels rely on the services provided by lower levels**.
    
- **Lower levels operate on a finer time scale**, often in the order of nanoseconds (10⁻⁹ seconds).
    
- **Changes in one layer do not impact others**, promoting **modularity** and ease of maintenance.

|**Level**|**Name**|**Objects**|**Example Operations**|
|--:|---|---|---|
|13|Shell|User programming environment|Shell statements|
|12|User Processes|User processes|Quit, kill, suspend, resume|
|11|Directories|Directories|Create, destroy, attach, detach, search, list|
|10|Devices|Printers, displays, keyboards|Read, write, open, close|
|9|File System|Files|Create, destroy, open, close, read, write|
|8|Communications|Pipes|Create, destroy, open, close, read, write|
|7|Virtual Memory|Segments, pages|Read, write, fetch|
|6|Local Secondary Store|Blocks of data, device channels|Read, write, allocate, free|
|5|Primitive Processes|Primitive process, semaphores|Suspend, resume, wait, signal|
|4|Interrupts|Interrupt-handling|Invoke, mask, unmask, retry programs|
|3|Procedures|Call stack, display|Mark stack, call, return|
|2|Instruction Set|Evaluation stack, data|Load, store, add, subtract, branch (machine code)|
|1|Electronic Circuits|Registers, buses|Clear, transfer, activate|

---

### Developments Leading to Modern OS
##### 1. Microkernel Architecture

- **Monolithic Kernel**:
    - Large kernel including all core OS services: scheduling, file system, networking, device drivers, memory management.
        
    - All components run in a single address space.
        
- **Modern Trend: Microkernel**:
    - Only core services (e.g., IPC, scheduling) are in the kernel.
        
    - Other services (file system, device drivers, etc.) run as user-mode **servers**.
        
    - This approach decouples kernel and server development, Servers may be customized to specific application or environment requirements.

|Feature|Description|
|---|---|
|**Uniform Interface**|All services accessed via message passing.|
|**Extensibility**|New services can be added easily.|
|**Flexibility**|Services can be customized, added, or removed.|
|**Portability**|Kernel changes are minimal when porting to new hardware.|
|**Reliability**|Modular design simplifies testing and debugging.|
|**Distributed Support**|Local and remote servers accessed uniformly—ideal for distributed OS.|
![[Pasted image 20250515172445.png|center]]

##### 2. Multithreading
- **Definition**: A technique where a process is divided into multiple *threads* that can run concurrently.
    
- Threads share the same address space but have:
    - Their own **program counter (PC)**, **stack pointer**, and **execution state**.
        
- Increases application **modularity** and **responsiveness**.

**Components**:

|             | Description                                                                         |
| ----------- | ----------------------------------------------------------------------------------- |
| **Thread**  | A dispatchable unit of work; sequential and interruptible.                          |
| **Process** | A collection of one or more threads plus system resources (memory, files, devices). |

> **Benefits**: Allows better control over **timing**, improves **responsiveness**, and takes advantage of **CPU concurrency**.

##### 3. Symmetric Multiprocessing (SMP)
- Multiple processors share:
    
    - The same main memory.
        
    - I/O facilities.
        
- All processors can perform the same functions (no master-slave relationship).

> Enables true **parallel execution** of processes and threads.

##### 4. Distributed Operating Systems
- Provides a **single-system image** to the user:
    
    - Appears like one unified OS.
        
- Core Features:
    - Distributed file system.
        
    - Distributed memory illusion.
        
    - Seamless integration of geographically dispersed resources.
        

##### 5. Object-Oriented Operating System Design
- OS is developed using **object-oriented principles**.
    
- Benefits:
    - Easy to extend via **modular components**.
        
    - Encourages **customization** without compromising stability.
        
    - Simplifies the development of **distributed systems**.

