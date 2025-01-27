Stage 1: Calling fopen()
Mode: User Mode (Unprivileged CPU)
The program is executing in user space, and the CPU is in user mode.
Memory: All memory belongs to the operating system. The kernel stack is reserved for the OS, and your program operates in its own memory (user stack).
Instruction Pointer (IP): Points to the shared library (glibc) implementation of fopen().
Stack Pointer (SP): Still points to the user stack, where function arguments, return addresses, and local variables are stored.
What Happens Here?
The program thread begins executing the fopen() function from the glibc shared library.
The glibc implementation prepares everything necessary for interacting with the operating system, including packing arguments into the correct format.

Stage 2: Transition to linux-gate.so
Mode: Still User Mode (Unprivileged CPU)
The glibc library relies on a system helper file, linux-gate.so, to act as a bridge between user space and the kernel.
What is linux-gate.so?
linux-gate.so is a small, shared object file created by the operating system. It is loaded into the program's address space at runtime (near the top of memory).
Purpose:
Sets up the required arguments in CPU registers.
Prepares for the system call by packaging everything for the kernel.
Triggers the SYSENTER instruction to transition to the kernel.

Stage 3: SYSENTER (System Call Entry)
Mode: Switching from User Mode to Kernel Mode
At this point, the SYSENTER instruction is executed by linux-gate.so. This is the moment the CPU transitions into privileged kernel mode.
What Happens During SYSENTER?
Registers Setup:
The operating system reloads the status registers to switch context from the user process to the kernel. For example, the kernel stack pointer and the instruction pointer are updated.

Memory Switch:

The kernel stack (not visible to user processes) is now active.
The page tables are adjusted, and the CPU now has access to kernel memory.
Kernel Execution:
The kernel begins executing the system call (e.g., open()) in privileged mode, directly interacting with hardware if necessary (e.g., accessing a file system or storage device).

Stage 4: Kernel Executes the System Call
Mode: Kernel Mode (Privileged CPU)
The kernel executes the system call open():
Stack Pointer (SP): Points to the kernel stack.
Instruction Pointer (IP): Points to kernel code that handles the system call.

Stage 5: Returning to User Space
Mode: Switching from Kernel Mode to User Mode
Once the kernel finishes executing the system call, it prepares the return data and restores the user process.
Key Steps:
Registers Restored:
The CPU restores the user process’s saved state (e.g., instruction pointer, stack pointer).

Memory Switch:
The SP switches back to the user stack. The kernel stack is no longer active for this process.

Execution Resumes:
The CPU returns to user mode, and the instruction pointer resumes execution in the glibc library, which completes the fopen() function.


关于指令如何从fopen（）变到open（）
Your program calls fopen().
glibc translates fopen() into a system call (open()).
The CPU switches from user mode to kernel mode, and the kernel stack processes the open() request.
The kernel stack executes the operation and returns the result to glibc.
glibc provides the final result (FILE*) back to your program.
