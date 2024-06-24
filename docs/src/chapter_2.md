# 第二章 Linux内核模块

## 本章要求

1. 了解Linux内核模块的概念和用法。
2. 掌握编写一个简单内核模块的方法。
3. 了解内核模块参数的传递方式。
4. 学会利用内核模块创建一个虚拟字符设备。
5. 了解如何通过数组参数传递给内核模块。

## 前提条件
要跟随本教程，您需要具备以下前提条件：
- 熟练掌握C编程
- 基本的Linux命令行知识
- 已设置好Linux开发环境（例如，运行Linux发行版并安装了`make`和`gcc`）

## 内核模块简介
Linux内核模块（LKM）是一段代码，可以加载到内核中以扩展其功能，而无需重新启动系统。模块通常用于设备驱动程序，可以动态加载和卸载。

## 编写一个简单的内核模块
从一个简单的“Hello, World!”内核模块开始，了解模块编程的基础。

### hello_module.c
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

### 编译与加载模块
1. 在目录中编写`Makefile`:
    ```makefile
    obj-m += hello_module.o

    all:
        make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

    clean:
        make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
    ```

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

##  内核模块传残

### param_module.c
```c
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/moduleparam.h>

static int param_var = 0;
module_param(param_var, int, S_IRUGO);
MODULE_PARM_DESC(param_var, "An integer");

static int __init param_init(void)
{
    printk(KERN_INFO "param_var: %d\n", param_var);
    return 0;
}

static void __exit param_exit(void)
{
    printk(KERN_INFO "Exiting param_module\n");
}

module_init(param_init);
module_exit(param_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("A module with parameter passing");
```

加载模块并传入参数
```bash
sudo insmod param_module.ko param_var=42
dmesg | tail
```

##  内核模块传入一个数组作为参数

Linux 内核模块可以接受数组作为参数，这对于配置和自定义模块行为非常有用。下面我们展示如何实现这一点。

### array_param_module.c
```c
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/moduleparam.h>

#define MAX_ARRAY_SIZE 5

// Define an array and a variable to hold the array size
static int array[MAX_ARRAY_SIZE];
static int arr_argc = 0;

// Define the module parameters
module_param_array(array, int, &arr_argc, 0000);
MODULE_PARM_DESC(array, "An integer array");

static int __init array_param_init(void)
{
    int i;
    printk(KERN_INFO "Array parameter module initialized with %d elements\n", arr_argc);
    for (i = 0; i < arr_argc; i++)
    {
        printk(KERN_INFO "array[%d] = %d\n", i, array[i]);
    }
    return 0;
}

static void __exit array_param_exit(void)
{
    printk(KERN_INFO "Exiting array parameter module\n");
}

module_init(array_param_init);
module_exit(array_param_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("A module with array parameter passing");
```

加载模块并传递数组参数：

    ```bash
    sudo insmod array_param_module.ko array=1,2,3,4,5
    ```

### 总结
通过这种方式，您可以向内核模块传递数组参数，从而使模块更加灵活和可配置。这对于需要动态传递多个参数的情况非常有用。

##  创建虚拟字符设备
虚拟字符设备通过文件操作与用户空间应用程序交互。


### virtual_char_dev.c
```c
#include <linux/module.h>
#include <linux/fs.h>
#include <linux/uaccess.h>  // copy_to_user, copy_from_user
#include <linux/cdev.h>

#define DEVICE_NAME "vchardev"
#define BUF_LEN 80

static int major;
static char message[BUF_LEN];
static struct class *vchardev_class = NULL;
static struct device *vchardev_device = NULL;

static int dev_open(struct inode *inode, struct file *file)
{
    printk(KERN_INFO "vchardev: device opened\n");
    return 0;
}

static int dev_release(struct inode *inode, struct file *file)
{
    printk(KERN_INFO "vchardev: device closed\n");
    return 0;
}

static ssize_t dev_read(struct file *file, char __user *buffer, size_t len, loff_t *offset)
{
    int bytes_read = 0;
    if (*message == 0)
        return 0;

    while (len && *message)
    {
        put_user(*(message++), buffer++);
        len--;
        bytes_read++;
    }
    printk(KERN_INFO "vchardev: read %d bytes\n", bytes_read);
    return bytes_read;
}

static ssize_t dev_write(struct file *file, const char __user *buffer, size_t len, loff_t *offset)
{
    int i;
    for (i = 0; i < len && i < BUF_LEN - 1; i++)
        get_user(message[i], buffer + i);

    message[i] = '\0';
    printk(KERN_INFO "vchardev: received %zu characters from user\n", len);
    return len;
}

static struct file_operations fops = {
    .open = dev_open,
    .read = dev_read,
    .write = dev_write,
    .release = dev_release,
};

static int __init vchardev_init(void)
{
    major = register_chrdev(0, DEVICE_NAME, &fops);
    if (major < 0)
    {
        printk(KERN_ALERT "vchardev failed to register a major number\n");
        return major;
    }
    printk(KERN_INFO "vchardev: registered with major number %d\n", major);

    vchardev_class = class_create(THIS_MODULE, DEVICE_NAME);
    if (IS_ERR(vchardev_class))
    {
        unregister_chrdev(major, DEVICE_NAME);
        printk(KERN_ALERT "Failed to register device class\n");
        return PTR_ERR(vchardev_class);
    }
    printk(KERN_INFO "vchardev: device class registered\n");

    vchardev_device = device_create(vchardev_class, NULL, MKDEV(major, 0), NULL, DEVICE_NAME);
    if (IS_ERR(vchardev_device))
    {
        class_destroy(vchardev_class);
        unregister_chrdev(major, DEVICE_NAME);
        printk(KERN_ALERT "Failed to create the device\n");
        return PTR_ERR(vchardev_device);
    }
    printk(KERN_INFO "vchardev: device created\n");

    return 0;
}

static void __exit vchardev_exit(void)
{
    device_destroy(vchardev_class, MKDEV(major, 0));
    class_unregister(vchardev_class);
    class_destroy(vchardev_class);
    unregister_chrdev(major, DEVICE_NAME);
    printk(KERN_INFO "vchardev: module unloaded\n");
}

module_init(vchardev_init);
module_exit(vchardev_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("A simple virtual character device");
```

##  开始计时器
计时器对于在内核空间中处理定时操作至关重要。


### timer_module.c
```c
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/timer.h>

static struct timer_list my_timer;

void timer_callback(struct timer_list *t)
{
    printk(KERN_INFO "Timer expired and callback executed\n");
}

static int __init timer_init(void)
{
    printk(KERN_INFO "Initializing Timer Module\n");

    timer_setup(&my_timer, timer_callback, 0);
    mod_timer(&my_timer, jiffies + msecs_to_jiffies(2000));  // 2 seconds

    return 0;
}

static void __exit timer_exit(void)
{
    del_timer(&my_timer);
    printk(KERN_INFO "Timer Module Unloaded\n");
}

module_init(timer_init);
module_exit(timer_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("A simple timer module");
```

##  实现简单的文件操作函数
### filp_open
`filp_open` 用于在内核空间打开一个文件。


### filp_open_example.c
```c
#include <linux/module.h>
#include <linux/kernel.h>
#include <linux/fs.h>
#include <linux/file.h>
#include <linux/slab.h>

static int __init filp_open_example_init(void)
{
    struct file *f;
    char buf[128];
    mm_segment_t fs;
    loff_t pos = 0;

    printk(KERN_INFO "Opening file from kernel space\n");
    
    f = filp_open("/etc/hostname", O_RDONLY, 0);
    if (IS_ERR(f))
    {
        printk(KERN_ALERT "Failed to open file\n");
        return PTR_ERR(f);
    }

    fs = get_fs();
    set_fs(KERNEL_DS);
    vfs_read(f, buf, sizeof(buf), &pos);
    set_fs(fs);

    printk(KERN_INFO "File content: %s\n", buf);

    filp_close(f, NULL);

    return 0;
}

static void __exit filp_open_example_exit(void)
{
    printk(KERN_INFO "Unloading filp_open_example module\n");
}

module_init(filp_open_example_init);
module_exit(filp_open_example_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("A module demonstrating filp_open");
```

## 结论
本教程为编写 Linux 内核模块提供了一个起点。它涵盖了模块参数传递、创建虚拟字符设备、启动定时器以及使用简单的文件操作。对于更高级的主题，请参考《Linux 内核模块编程指南》和其他内核开发资源。

## 参考文献

- [Linux Kernel Module Programming Guide](https://tldp.org/LDP/lkmpg/2.6/html/)
- [Linux Device Drivers, Third Edition](https://lwn.net/Kernel/LDD3/)
- [Kernel API Documentation](https://www.kernel.org/doc/html/latest/)
