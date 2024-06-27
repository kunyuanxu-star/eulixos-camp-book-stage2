# 控制结构的汇编表示方法

C语言与汇编往往呈现一一对应关系，但控制结构的汇编表示方法却有所不同。本节将介绍C语言控制结构的汇编表示方法，以大家最为熟悉的C语言结构——循环、分支、跳转为例，介绍如何用汇编语言表示这些控制结构。

## 内联汇编语法

在GCC编译器中，内联汇编使用`asm`关键字。以下是一个基本的内联汇编语法示例：

```c
int result;
asm("add %0, %1, %2" : "=r" (result) : "r" (a), "r" (b));
```

- `"add %0, %1, %2"`：汇编指令模板。
- `: "=r" (result)`：输出操作数，`=r`表示输出到寄存器。
- `: "r" (a), "r" (b)`：输入操作数，`r`表示从寄存器读取。

在上面的示例中，`result`变量的值等于`a`和`b`的和。

## 控制结构的内联汇编表示

### while循环
```c
int i = 0;
while (i < 10) {
    // 循环体
    i++;
}
```

等价的内联汇编：
```c
int i = 0;
asm volatile (
    "loop_start:            \n\t"
    "   blt  %[i], %[max], loop_body \n\t"
    "   j    loop_end       \n\t"
    "loop_body:             \n\t"
    "   addi %[i], %[i], 1  \n\t"
    "   j    loop_start     \n\t"
    "loop_end:              \n\t"
    : [i] "+r" (i)
    : [max] "r" (10)
    : "cc"
);
```

### if-else语句
```c
int a = 5, b = 10;
if (a < b) {
    // 真分支
} else {
    // 假分支
}
```

等价的内联汇编：
```c
int a = 5, b = 10;
asm volatile (
    "   blt   %[a], %[b], if_true  \n\t"
    "   j     else_branch          \n\t"
    "if_true:                      \n\t"
    // 真分支代码
    "   j     end_if               \n\t"
    "else_branch:                  \n\t"
    // 假分支代码
    "end_if:                       \n\t"
    :
    : [a] "r" (a), [b] "r" (b)
    : "cc"
);
```

### do-while循环
```c
int i = 0;
do {
    // 循环体
    i++;
} while (i < 10);
```

等价的内联汇编：
```c
int i = 0;
asm volatile (
    "do_start:                \n\t"
    "   addi %[i], %[i], 1    \n\t"
    "   blt  %[i], %[max], do_start \n\t"
    : [i] "+r" (i)
    : [max] "r" (10)
    : "cc"
);
```

### switch语句
```c
int val = 2;
switch (val) {
    case 1:
        // case 1代码
        break;
    case 2:
        // case 2代码
        break;
    default:
        // 默认代码
        break;
}
```

等价的内联汇编：
```c
int val = 2;
asm volatile (
    "   li    t0, 1           \n\t"
    "   beq   %[val], t0, case1 \n\t"
    "   li    t0, 2           \n\t"
    "   beq   %[val], t0, case2 \n\t"
    "   j     default_case    \n\t"
    "case1:                   \n\t"
    // case 1代码
    "   j     end_switch      \n\t"
    "case2:                   \n\t"
    // case 2代码
    "   j     end_switch      \n\t"
    "default_case:            \n\t"
    // 默认代码
    "end_switch:              \n\t"
    :
    : [val] "r" (val)
    : "t0", "cc"
);
```

### continue与break
在循环中使用`continue`和`break`：

```c
for (int i = 0; i < 10; i++) {
    if (i == 5) continue;
    if (i == 8) break;
    // 循环体
}
```

等价的内联汇编：
```c
int i = 0;
asm volatile (
    "for_start:                \n\t"
    "   bge   %[i], %[max], for_end   \n\t"
    "   li    t0, 5           \n\t"
    "   beq   %[i], t0, continue_loop \n\t"
    "   li    t0, 8           \n\t"
    "   beq   %[i], t0, for_end       \n\t"
    // 循环体代码
    "continue_loop:           \n\t"
    "   addi  %[i], %[i], 1   \n\t"
    "   j     for_start       \n\t"
    "for_end:                 \n\t"
    : [i] "+r" (i)
    : [max] "r" (10)
    : "t0", "cc"
);
```
