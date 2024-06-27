# 实现简单的文件操作函数

## filp_open函数及示例
`filp_open` 用于在内核空间打开一个文件。

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

## 加载与调试

编译并加载模块：

```
$ make
$ sudo insmod filp_open_example.ko
```


运行结果：

```
[   26.313563] Opening file from kernel space
[   26.313602] File content: localhost
[   26.313605] Unloading filp_open_example module
```
