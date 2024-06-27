# 内核模块传参

## 示例
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

# 数组传参

Linux 内核模块可以接受数组作为参数，这对于配置和自定义模块行为非常有用。下面我们展示如何实现这一点。

## 示例
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

## 总结
通过这种方式，可以向内核模块传递数组参数，从而使模块更加灵活和可配置。这对于需要动态传递多个参数的情况非常有用。
