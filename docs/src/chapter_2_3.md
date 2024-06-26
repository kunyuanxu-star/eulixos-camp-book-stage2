## 创建虚拟字符设备

虚拟字符设备通过文件操作与用户空间应用程序交互。


## 示例代码
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

## 编译运行调试

编译代码：

```bash
$ make
```

加载模块：

```bash
$ insmod vchardev.ko
```

查看模块信息：

```bash
$ lsmod | grep vchardev
vchardev 16384 0
```

创建设备文件：

```bash
$ mknod /dev/vchardev c 254 0
```

查看设备文件信息：

```bash
$ ls -l /dev/vchardev
crw-rw-rw- 1 root root 254, 0 May 20 10:20 /dev/vchardev
```

打开设备文件：

```bash
$ cat /dev/vchardev
```

向设备文件写入数据：

```bash
$ echo "Hello, world!" > /dev/vchardev
```

从设备文件读取数据：

```bash
$ cat /dev/vchardev
Hello, world!
```

卸载模块：

```bash
$ rmmod vchardev
```
