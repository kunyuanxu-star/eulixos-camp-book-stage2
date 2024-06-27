# 编写一个简单的内核模块

从一个简单的“Hello, World!”内核模块开始，了解模块编程的基础。

## 示例
```c
#include <linux/module.h>  // Needed by all modules
#include <linux/kernel.h>  // Needed for KERN_INFO
#include <linux/init.h>    // Needed for macros

static int __init hello_init(void)
{
    printk(KERN_INFO "Hello, World!\n");
    return 0;
}

static void __exit hello_exit(void)
{
    printk(KERN_INFO "Goodbye, World!\n");
}

module_init(hello_init);
module_exit(hello_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("A simple Hello World module");
```

## 编译与加载模块
1. 在目录中编写`Makefile`:
    ```makefile
    obj-m += hello_module.o

    all:
        make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

    clean:
        make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
    ```

> 注意：之后其他章节均有类似此示例的Makefile，故省略不写，Makefile中`obj-m`变量的值应与模块名一致。

2. 编译模块:
    ```bash
    make
    ```

3. 加载模块:
    ```bash
    sudo insmod hello_module.ko
    ```

4. 查看模块加载日志:
    ```bash
    dmesg | tail
    ```

5. 卸载模块:
    ```bash
    sudo rmmod hello_module
    ```