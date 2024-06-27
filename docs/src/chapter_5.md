# Linux系统编程基础

## 本章要求

1. 了解Linux系统编程的基本概念，包括进程、线程、内存管理、文件系统接口等。
2. 掌握Linux系统编程的基本技术，包括获取主机名、设置程序内存上限、使用`fork`创建子进程、通过管道进行进程间通讯等。
3. 能够编写简单的Linux系统编程程序。

## 注意

本章节为习题前置知识，而非正式课程内容，但建议先学习本章节内容再进行相关习题。

## 介绍

Linux系统编程涉及使用C语言与操作系统进行直接交互。本文将详细介绍一些基本的Linux系统编程技术，并通过示例代码展示如何获取主机名、设置程序内存上限、使用`fork`创建子进程，以及通过管道进行进程间通讯。

## 获取主机名

### `gethostname`函数

`gethostname`函数用于获取当前主机的名称。它的原型如下：

```c
#include <unistd.h>
int gethostname(char *name, size_t len);
```

### 示例代码

```c
#include <stdio.h>
#include <unistd.h>
#include <limits.h>

int main() {
    char hostname[HOST_NAME_MAX];
    
    if (gethostname(hostname, sizeof(hostname)) == -1) {
        perror("gethostname");
        return 1;
    }

    printf("Hostname: %s\n", hostname);
    return 0;
}
```

## 使用`rlimit`设置程序内存上限

### `setrlimit`函数

`setrlimit`函数用于设置进程的资源限制。它的原型如下：

```c
#include <sys/resource.h>
int setrlimit(int resource, const struct rlimit *rlim);
```

### 示例代码

```c
#include <stdio.h>
#include <sys/resource.h>

int main() {
    struct rlimit rl;
    
    // 设置程序的内存上限为128MB
    rl.rlim_cur = 128 * 1024 * 1024;
    rl.rlim_max = 128 * 1024 * 1024;
    
    if (setrlimit(RLIMIT_AS, &rl) == -1) {
        perror("setrlimit");
        return 1;
    }

    printf("Memory limit set to 128MB\n");
    return 0;
}
```

## `fork`函数创建子进程

### `fork`函数

`fork`函数用于创建一个子进程。它的原型如下：

```c
#include <unistd.h>
pid_t fork(void);
```

### 示例代码

```c
#include <stdio.h>
#include <unistd.h>

int main() {
    pid_t pid = fork();

    if (pid == -1) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // 子进程
        printf("Hello from the child process!\n");
    } else {
        // 父进程
        printf("Hello from the parent process!\n");
    }

    return 0;
}
```

## 管道与进程间通讯

### `pipe`函数

`pipe`函数用于创建一个管道，管道是进程间通讯的一种方式。它的原型如下：

```c
#include <unistd.h>
int pipe(int pipefd[2]);
```

### 示例代码

```c
#include <stdio.h>
#include <unistd.h>
#include <string.h>

int main() {
    int pipefd[2];
    char buffer[128];

    if (pipe(pipefd) == -1) {
        perror("pipe");
        return 1;
    }

    pid_t pid = fork();

    if (pid == -1) {
        perror("fork");
        return 1;
    } else if (pid == 0) {
        // 子进程
        close(pipefd[0]); // 关闭读端
        char message[] = "Hello from the child process!";
        write(pipefd[1], message, strlen(message));
        close(pipefd[1]); // 关闭写端
    } else {
        // 父进程
        close(pipefd[1]); // 关闭写端
        read(pipefd[0], buffer, sizeof(buffer));
        printf("Parent received: %s\n", buffer);
        close(pipefd[0]); // 关闭读端
    }

    return 0;
}
```

## 学习资料

学习Linux系统编程的相关资料和教程非常丰富，以下是一些推荐的资源：

### 书籍
1. **《UNIX环境高级编程》**（Advanced Programming in the UNIX Environment） - 作者：W. Richard Stevens
   - 这本书被誉为UNIX编程的经典之作，详细介绍了UNIX系统编程的各个方面，是深入学习Linux编程的必备书籍。

2. **《Linux系统编程》**（Linux System Programming） - 作者：Robert Love
   - 本书专门介绍了Linux系统编程的基础和高级主题，涵盖了文件I/O、进程控制、线程和同步、网络编程等内容。

3. **《深入理解Linux内核》**（Understanding the Linux Kernel） - 作者：Daniel P. Bovet, Marco Cesati
   - 这本书深入解析了Linux内核的各个子系统和模块，对理解Linux系统的工作原理非常有帮助。

### 在线教程和文档
1. **The Linux Programming Interface** - 作者：Michael Kerrisk
   - 该书的官方网站提供了部分章节的在线阅读和示例代码，详细讲解了Linux编程接口。

2. [**GNU C Library Documentation**](https://www.gnu.org/software/libc/manual/)
   - 官方文档详细介绍了GNU C库中的各个函数和数据结构，是查阅具体函数用法的权威资料。

3. **Linux Manual Pages (man pages)**
   - Linux系统自带的手册页提供了大量函数和命令的使用说明，使用`man`命令可以方便地查阅。
   - 例如，`man 2 fork` 可以查看`fork`系统调用的详细信息。

### 在线课程
1. [**Coursera: "Operating Systems and You: Becoming a Power User"**]((https://www.coursera.org/learn/os-power-user))
   - 这门课程由谷歌提供，介绍了Linux操作系统的基本使用和系统编程的基本概念。

2. [**Udacity: "Linux System Programming"**](https://www.udacity.com/course/linux-system-programming--ud923)
   - 这门课程提供了Linux系统编程的入门知识，适合初学者学习。

### 社区和论坛
1. [**Stack Overflow**](https://stackoverflow.com/)
   - Stack Overflow是一个大型编程问答社区，里面有大量关于Linux系统编程的问答，搜索具体问题时可以找到许多有用的答案。

2. [**Reddit: r/linux**](https://www.reddit.com/r/linux/)
   - Reddit的Linux板块讨论Linux系统相关的各种话题，包括系统编程，里面有很多经验丰富的用户可以提供帮助。

### 实验和实践
1. [**Linux From Scratch (LFS)**](http://www.linuxfromscratch.org/lfs/)
   - 通过从零开始构建一个Linux系统，可以深入理解Linux的各个组件和编程接口。

