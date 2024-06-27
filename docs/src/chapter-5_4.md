# 实现计时器

计时器对于在内核空间中处理定时操作至关重要。


## 示例
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

## 编译运行调试

加载模块：

```bash
sudo insmod timer.ko
```

卸载模块：

```bash
sudo rmmod timer
```

查看日志：

```bash
dmesg
```

在日志中可以看到打印的时间信息。
