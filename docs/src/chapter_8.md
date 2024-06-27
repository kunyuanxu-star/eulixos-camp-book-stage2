# RISC-V向量拓展基础与优化基础C库实践

## 本章要求

1. 掌握RISC-V向量拓展基础知识
2. 掌握RISC-V向量拓展优化基础知识
3. 掌握C语言中向量化编程的基本方法
4. 掌握C语言中向量化编程的优化方法

## 主要内容

- [RISC-V 向量拓展 (V Extension) 基础](./chapter_8_1.md)
- [RISC-V SIMD 和 Unroll 优化方案](./chapter_8_2.md)

## 参考资料

1. **RISC-V指令集架构手册** - 这是理解RISC-V架构的基础，包括其向量扩展和SIMD能力。
   - 链接：[RISC-V ISA Manual](https://riscv.org/technical/specifications/)

2. **RISC-V Vector Extension Specification** - 详细介绍了RISC-V的向量扩展，包括指令集和编程模型。
   - 链接：[RISC-V Vector Extension](https://github.com/riscv/riscv-v-spec)

3. **GCC编译器文档** - 包括如何使用GCC编译器进行RISC-V程序的编译和优化。
   - 链接：[GCC RISC-V Options](https://gcc.gnu.org/onlinedocs/gcc-9.2.0/gcc/RISC-V-Options.html)

4. **LLVM编译器文档** - LLVM项目也支持RISC-V，文档中包含了编译和优化RISC-V程序的相关信息。
   - 链接：[LLVM RISC-V Documentation](https://llvm.org/docs/RISCV.html)

5. **RISC-V Software Development Tools** - 介绍RISC-V软件开发工具，包括编译器、调试器和性能分析工具。
   - 链接：[RISC-V Software Tools](https://riscv.org/software-tools/)

6. **RISC-V International Conferences** - RISC-V相关的国际会议论文集，包含最新的研究成果和开发实践。
   - 链接：[RISC-V International Conferences](https://riscv.org/conference/)

7. **RISC-V社区论坛和邮件列表** - 社区讨论和技术支持，可以获取关于RISC-V编程和优化的实用建议。
   - 链接：[RISC-V Community](https://riscv.org/community/)

8. **性能分析工具文档** - 如gprof和perf，这些工具可以帮助你分析程序的性能并识别优化点。
   - gprof文档：通常包含在GCC的文档中。
   - perf文档：[perf Documentation](https://www.kernel.org/doc/html/latest/dev-tools/perf.html)

9. **并行编程和优化书籍** - 这些书籍通常包含关于如何优化并行程序的通用策略，可以应用于RISC-V SIMD优化。
   - 例如："Parallel Programming for Multicore and Cluster Systems" by Mikel L. Z. Luque

10. **学术期刊和会议论文** - 搜索与RISC-V SIMD和Unroll优化相关的学术论文，可以提供深入的技术分析和案例研究。

请注意，上述链接可能需要根据实际可用性和访问权限进行调整。在使用这些资源时，请确保遵循适当的引用规范和版权法规。
