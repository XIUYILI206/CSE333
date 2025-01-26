Stream 是对 POSIX 的封装，提供了更高层次的抽象，使用更简单。
POSIX I/O 是直接与操作系统对话，提供更精细的控制，适合对性能要求高的场景

#include <stdio.h>

int main() {
    FILE* file = fopen("example.txt", "w");
    fprintf(file, "Hello, stream!\n"); // 写入文件
    fclose(file);
    return 0;
}


#include <fcntl.h>
#include <unistd.h>

int main() {
    int fd = open("example.txt", O_WRONLY | O_CREAT, 0644);
    write(fd, "Hello, POSIX I/O!\n", 18); // 写入文件
    close(fd);
    return 0;
}

POSIX I/O routines
1）主要用于file description
2）作用：posix 是一个标准化接口，许多操作系统（如 Linux、macOS 和其他类 Unix 系统）都支持它。使用 POSIX I/O routines 编写的代码可以在不同平台上运行，而无需修改代码。
3）我们直接和operating system对话的是opsix而不是c语言。它提供了程序与操作系统交互的方法。我们用 POSIX I/O 函数（如 open()、read()、write() 等）与操作系统直接对话，从而访问文件、设备或者其他硬件资源。

Stream:
c streams are manipulated with a FILE* pointer, which is defined in stdio.h

