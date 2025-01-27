假设硬件是银行金库。
操作系统是银行的保险系统和柜员。
用户进程是客户

os isolates process from each other
操作系统的 隔离机制 的确会确保两个不同的应用程序（app）之间不能相互访问对方的内存或资源，除非经过操作系统的允许（比如共享文件、网络连接等）。
客户（用户进程）不能直接进入金库（硬件），只能通过柜员（操作系统）提出请求，比如取钱或存钱。

OS isolates itself from processes
如果一个程序直接控制硬盘读写，它可能会覆盖操作系统的关键文件，导致系统崩溃。
柜员会检查客户的身份（权限验证），确保合法后，再从金库里操作取钱或存钱

OS is allowed to access the hardware
柜员自己有权限直接访问金库（硬件），但不会允许客户随意进入。
——————————————————————————————————————————————————————————————————————————————————————————
Process A（Untrusted）：

这是用户程序，比如你运行的一个应用程序。
它被标记为 Untrusted（不可信），因为它运行在用户模式（User Mode），没有直接访问硬件的权限。
OS（Trusted）：

操作系统被认为是 Trusted（可信的），因为它运行在特权模式（Privileged Mode 或 Kernel Mode）。
它有权限直接与硬件交互，并对用户进程的行为进行管理。
HW（Trusted）：

硬件层（如 CPU、内存、硬盘）是受信任的，但它只能信任操作系统，而不会直接信任用户进程。
System Call Trace（系统调用轨迹）：

当用户程序需要访问硬件（例如读文件或网络操作）时，它必须通过 系统调用（System Call） 请求操作系统帮忙完成任务。
系统调用会将控制权从用户进程转交给操作系统。操作完成后，控制权会回到用户进程，继续执行。
——————————————————————————————————————————————————————————————————————————————————————————
用户程序无法直接访问硬件：
用户程序运行在用户模式（Mode = 0），需要通过系统调用请求 OS 访问硬件。

硬件信任操作系统：
硬件直接信任操作系统（因为 OS 运行在内核模式，用户程序Mode = 1）。

——————————————————————————————————————————————————————————————————————————————————————————
总结：
流程解读：
用户模式（Mode = 0）开始：

你的程序运行在用户模式。
当你调用 POSIX 函数（如 read()）时，程序依然运行在用户模式。
触发系统调用（System Call）：

POSIX 函数内部通过软中断（Software Interrupt）或其他机制，发起系统调用。
系统调用进入操作系统的内核部分，CPU 模式切换到 内核模式（Mode = 1）。
内核模式（Mode = 1）执行：

在内核模式下，操作系统接管，直接与硬件交互（例如从硬盘读取数据）。
返回用户模式（Mode = 0）：

系统调用完成后，操作系统将结果返回给用户程序。
CPU 切回用户模式，继续执行你的程序。


Making a System Call:

When a process in user mode calls a system call (e.g., read(), write()), the CPU switches from user mode to kernel mode.
This is done using a special instruction (e.g., syscall on x86-64 architecture).
Operating System Executes the Request:

In kernel mode, the operating system takes control of the CPU and executes the requested operation (e.g., reading a file).
The process cannot control what happens in kernel mode, ensuring security and stability.
Returning to User Mode:

Once the operating system completes the system call, the CPU switches back to user mode.
The process can then resume its execution in user mode with the result of the system call.
+++++++++++++++++++++++++++++++++++++++++++++++++++++
误区：
POSIX 函数本身并不切换模式。

POSIX 函数运行在 用户模式（Mode = 0） 下，它是一个高级的 API 封装。
当 POSIX 函数需要操作系统的帮助时（比如文件操作），它会触发一个 系统调用（System Call）。
系统调用会切换到内核模式（Mode = 1）。

系统调用是 POSIX 函数的底层实现，当用户程序调用系统调用时，CPU 会从 用户模式（Mode = 0） 切换到 内核模式（Mode = 1）。
所以，模式切换发生在系统调用的过程中，而不是 POSIX 函数调用的过程中
