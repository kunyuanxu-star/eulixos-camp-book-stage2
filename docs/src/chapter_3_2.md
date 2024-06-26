# RISC-V基础指令集

本集训营用的最多的指令集是RV64I基础，它包含了处理器指令集最核心的部分:整数计算、控制流和内存访问指令，指令简单明了，适合快速掌握。

下面是对 RV64I 基础指令集的详细介绍。

## 寄存器
- `x0`-`x31`：32个整数寄存器。
- `f0`-`f31`：32个浮点寄存器。
- `pc`：程序计数器。
- `sp`：堆栈指针。
- `ra`：返回地址寄存器。

## 指令格式

指令格式如下：

```assembly
opcode rd, rs1, rs2 [, imm]
```

- `opcode`：指令的操作码。
- `rd`：目标寄存器。
- `rs1`：源寄存器1。
- `rs2`：源寄存器2。
- `imm`：立即数。

## 指令集

### RV32I 基础指令集

RV32I 基础指令集包含了整数计算、控制流和内存访问指令。

- 整数计算指令：`ADD`, `SUB`, `ADDI`, `AND`, `OR`, `XOR`, `ANDI`, `SLL`, `SRL`, `SRA`, `SLT`, `SLTU`, `SLTI`
- 控制流指令：`BEQ`, `BNE`, `BLT`, `BGE`, `JAL`, `JALR`
- 内存访问指令：`LW`, `LH`, `LHU`, `LB`, `LBU`, `SW`, `SH`, `SB`

## 整数计算指令

### 算术指令
- **ADD**: 将两个寄存器的值相加，结果存储在目标寄存器中。
  ```assembly
  ADD rd, rs1, rs2  # rd = rs1 + rs2
  ```
- **SUB**: 将一个寄存器的值从另一个寄存器的值中减去，结果存储在目标寄存器中。
  ```assembly
  SUB rd, rs1, rs2  # rd = rs1 - rs2
  ```
- **ADDI**: 将一个立即数与寄存器的值相加，结果存储在目标寄存器中。
  ```assembly
  ADDI rd, rs1, imm  # rd = rs1 + imm
  ```

### 逻辑指令
- **AND**: 对两个寄存器的值执行按位与操作，结果存储在目标寄存器中。
  ```assembly
  AND rd, rs1, rs2  # rd = rs1 & rs2
  ```
- **OR**: 对两个寄存器的值执行按位或操作，结果存储在目标寄存器中。
  ```assembly
  OR rd, rs1, rs2  # rd = rs1 | rs2
  ```
- **XOR**: 对两个寄存器的值执行按位异或操作，结果存储在目标寄存器中。
  ```assembly
  XOR rd, rs1, rs2  # rd = rs1 ^ rs2
  ```
- **ANDI**: 将寄存器的值与一个立即数进行按位与操作，结果存储在目标寄存器中。
  ```assembly
  ANDI rd, rs1, imm  # rd = rs1 & imm
  ```

### 移位指令
- **SLL**: 将寄存器的值左移指定的位数，结果存储在目标寄存器中。
  ```assembly
  SLL rd, rs1, rs2  # rd = rs1 << rs2
  ```
- **SRL**: 将寄存器的值右移指定的位数，结果存储在目标寄存器中（逻辑右移）。
  ```assembly
  SRL rd, rs1, rs2  # rd = rs1 >> rs2
  ```
- **SRA**: 将寄存器的值右移指定的位数，结果存储在目标寄存器中（算术右移）。
  ```assembly
  SRA rd, rs1, rs2  # rd = rs1 >> rs2 (arithmetic)
  ```

### 比较指令
- **SLT**: 如果第一个寄存器的值小于第二个寄存器的值，则目标寄存器设置为1，否则设置为0（有符号比较）。
  ```assembly
  SLT rd, rs1, rs2  # rd = (rs1 < rs2)
  ```
- **SLTU**: 如果第一个寄存器的值小于第二个寄存器的值，则目标寄存器设置为1，否则设置为0（无符号比较）。
  ```assembly
  SLTU rd, rs1, rs2  # rd = (rs1 < rs2) (unsigned)
  ```
- **SLTI**: 如果寄存器的值小于立即数，则目标寄存器设置为1，否则设置为0（有符号比较）。
  ```assembly
  SLTI rd, rs1, imm  # rd = (rs1 < imm)
  ```

## 控制流指令

### 条件分支指令
- **BEQ**: 如果两个寄存器的值相等，则跳转到目标地址。
  ```assembly
  BEQ rs1, rs2, offset  # if (rs1 == rs2) PC += offset
  ```
- **BNE**: 如果两个寄存器的值不相等，则跳转到目标地址。
  ```assembly
  BNE rs1, rs2, offset  # if (rs1 != rs2) PC += offset
  ```
- **BLT**: 如果第一个寄存器的值小于第二个寄存器的值，则跳转到目标地址（有符号比较）。
  ```assembly
  BLT rs1, rs2, offset  # if (rs1 < rs2) PC += offset
  ```
- **BGE**: 如果第一个寄存器的值大于或等于第二个寄存器的值，则跳转到目标地址（有符号比较）。
  ```assembly
  BGE rs1, rs2, offset  # if (rs1 >= rs2) PC += offset
  ```

### 无条件跳转指令
- **JAL**: 跳转到目标地址，并将返回地址存储在目标寄存器中。
  ```assembly
  JAL rd, offset  # rd = PC + 4; PC += offset
  ```
- **JALR**: 跳转到由寄存器和立即数计算出的地址，并将返回地址存储在目标寄存器中。
  ```assembly
  JALR rd, rs1, offset  # rd = PC + 4; PC = (rs1 + offset) & ~1
  ```

## 内存访问指令

### 加载指令
- **LW**: 从内存中加载一个字（32位）到目标寄存器。
  ```assembly
  LW rd, offset(rs1)  # rd = *(rs1 + offset)
  ```
- **LH**: 从内存中加载一个半字（16位）到目标寄存器，并进行符号扩展。
  ```assembly
  LH rd, offset(rs1)  # rd = *(int16_t *)(rs1 + offset)
  ```
- **LHU**: 从内存中加载一个半字（16位）到目标寄存器，并进行零扩展。
  ```assembly
  LHU rd, offset(rs1)  # rd = *(uint16_t *)(rs1 + offset)
  ```
- **LB**: 从内存中加载一个字节（8位）到目标寄存器，并进行符号扩展。
  ```assembly
  LB rd, offset(rs1)  # rd = *(int8_t *)(rs1 + offset)
  ```
- **LBU**: 从内存中加载一个字节（8位）到目标寄存器，并进行零扩展。
  ```assembly
  LBU rd, offset(rs1)  # rd = *(uint8_t *)(rs1 + offset)
  ```

### 存储指令
- **SW**: 将一个字（32位）从源寄存器存储到内存中。
  ```assembly
  SW rs2, offset(rs1)  # *(rs1 + offset) = rs2
  ```
- **SH**: 将一个半字（16位）从源寄存器存储到内存中。
  ```assembly
  SH rs2, offset(rs1)  # *(int16_t *)(rs1 + offset) = rs2
  ```
- **SB**: 将一个字节（8位）从源寄存器存储到内存中。
  ```assembly
  SB rs2, offset(rs1)  # *(int8_t *)(rs1 + offset) = rs2
  ```

这些指令构成了 RISC-V 基础指令集的重要部分，为各种计算和控制流操作提供了必要的功能。
