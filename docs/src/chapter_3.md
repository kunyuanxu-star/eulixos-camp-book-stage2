# 第三章 RISC-V架构内联汇编

## 介绍
内联汇编允许在C语言代码中直接嵌入汇编指令，从而能够利用底层硬件特性来优化性能。本教程将介绍如何在RISC-V架构上使用内联汇编，涵盖RISC-V指令集的基础知识，以及如何将C语言中的常见控制结构用汇编表示。

## RISC-V指令集简介
RISC-V（Reduced Instruction Set Computing V）是一种开源的指令集架构（ISA），它具有简洁、模块化和高效等特点。以下是一些基本的RISC-V指令：

### 寄存器
- `x0`-`x31`：32个整数寄存器。
- `f0`-`f31`：32个浮点寄存器。

### 基本指令
- `add rd, rs1, rs2`：rd = rs1 + rs2
- `sub rd, rs1, rs2`：rd = rs1 - rs2
- `mul rd, rs1, rs2`：rd = rs1 * rs2
- `div rd, rs1, rs2`：rd = rs1 / rs2
- `lw rd, offset(rs1)`：从内存加载字到rd
- `sw rs2, offset(rs1)`：将字存储到内存

## 内联汇编语法
在GCC编译器中，内联汇编使用`asm`关键字。以下是一个基本的内联汇编语法示例：

```c
int result;
asm("add %0, %1, %2" : "=r" (result) : "r" (a), "r" (b));
```

- `"add %0, %1, %2"`：汇编指令模板。
- `: "=r" (result)`：输出操作数，`=r`表示输出到寄存器。
- `: "r" (a), "r" (b)`：输入操作数，`r`表示从寄存器读取。

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

## 参考资料
- [RISC-V 官方文档](https://riscv.org/technical/specifications/)
- [GCC 内联汇编指南](https://gcc.gnu.org/onlinedocs/gcc/Using-Assembly-Language-with-C.html)
- [RISC-V 汇编教程](https://riscv.org/software-tools/risc-v-assembly-programming/)

## 相关教程
- 《RISC-V汇编语言程序设计》
- 《嵌入式系统与RISC-V架构》
- [Learn RISC-V Assembly](https://riscvasm.com)

通过本教程，你已经了解了如何在RISC-V架构上使用内联汇编，并掌握了将C语言中的控制结构转换为汇编代码的方法。希望这些内容对你在实际编程中有所帮助。
