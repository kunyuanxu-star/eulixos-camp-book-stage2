# 第一章 矩阵与卷积算法

## 本章要求

1. 了解矩阵乘法和卷积算法。
2. 掌握矩阵转置的概念。
3. 学会通过编程实现矩阵乘法和卷积算法。

##  矩阵与线性代数

-矩阵乘法是线性代数的基本运算，它可以用来表示和操作向量、矩阵、张量等数学对象。矩阵的加法、减法、乘法、除法、转置、行列式、特征值、特征向量等运算都是线性代数的基本概念。

矩阵乘算法流程如下：

1. 确定输入矩阵的维度。
2. 确定输出矩阵的维度。
3. 按照输出矩阵的维度，将输入矩阵的行向量依次与输出矩阵的列向量进行矩阵乘法运算，得到输出矩阵的相应元素。
4. 输出矩阵的元素即为输入矩阵与输出矩阵的乘积。

伪代码如下

1. 输入矩阵 A 的维度为 m * n，输出矩阵 B 的维度为 p * q。
2. 对于 i = 1 to p，j = 1 to q，执行以下操作：
   - 对于 k = 1 to n，执行以下操作：
     - 计算 A(i, k) * B(k, j) 并将结果累加到 C(i, j) 中。
   - 输出矩阵 C 的元素即为输入矩阵 A 和输出矩阵 B 的卷积。

示例代码（C语言）：

```c
// 假设我们有两个矩阵A和B，它们的尺寸分别为 m x n 和 n x p，矩阵C是结果矩阵，尺寸为 m x p。
void matrix_multiply(int m, int n, int p, double A[m][n], double B[n][p], double C[m][p]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < p; j++) {
            C[i][j] = 0;
            for (int k = 0; k < n; k++) {
                C[i][j] += A[i][k] * B[k][j];
            }
        }
    }
}
```


##  卷积算法

卷积算法是一种在信号处理、图像处理、机器学习等领域广泛使用的数学运算。它涉及到两个函数的卷积，通常用来分析一个信号或函数与另一个信号或函数的相似度。在深度学习中，卷积神经网络（CNN）就是基于卷积算法构建的。

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

<p>
The convolution of two functions \( f(t) \) and \( g(t) \), denoted as \( (f * g)(t) \), is defined as:
</p>
<p>
\[
        (f * g)(t) = \int_{-\infty}^{\infty} f(\tau) g(t - \tau) \, d\tau
        \]
</p>

其中，τ是积分变量，t是时间或空间的坐标。

在离散情况下，卷积定义为：
<p>
        \[
        (f * g)(n) = \sum_{m=-\infty}^{\infty} f(m) g(n - m)
        \]
    </p>
这里的f(m)和g(m)是离散信号的值。

### 卷积的应用

信号处理：用于滤波、平滑、特征提取等。
图像处理：用于边缘检测、图像平滑、特征提取等。
机器学习：在卷积神经网络中，卷积层通过卷积操作提取输入数据的特征。

### 卷积算法流程

1. 确定卷积核：选择或学习一个卷积核（滤波器）。
2. 卷积核中心定位：将卷积核中心对齐到输入数据的第一个位置。
3. 元素乘积：计算卷积核和输入数据对应位置的元素乘积。
4. 求和：将上一步得到的乘积求和，得到输出特征图的一个元素。
5. 滑动卷积核：将卷积核向右滑动一个元素宽度，重复步骤3和4。

### 资料
1. [知乎 - 如何通俗易懂地解释卷积?](https://www.zhihu.com/question/22298352)
2. [CSDN - 卷积算法详解](https://blog.csdn.net/weixin_43702653/article/details/123776987)

### 示例代码（C语言）

```c
// 假设我们有一个输入矩阵 input 和一个卷积核 kernel，我们需要计算输出矩阵 output。
void convolution2D(int inputRows, int inputCols, double input[inputRows][inputCols], 
                   int kernelRows, int kernelCols, double kernel[kernelRows][kernelCols], 
                   double output[inputRows][inputCols]) {
    int kernelCenterX = kernelCols / 2;
    int kernelCenterY = kernelRows / 2;

    for (int i = 0; i < inputRows; ++i) {
        for (int j = 0; j < inputCols; ++j) {
            output[i][j] = 0; // 初始化输出矩阵
            for (int m = 0; m < kernelRows; ++m) {
                for (int n = 0; n < kernelCols; ++n) {
                    int ii = i + (m - kernelCenterY);
                    int jj = j + (n - kernelCenterX);

                    // 检查是否越界
                    if (ii >= 0 && ii < inputRows && jj >= 0 && jj < inputCols) {
                        output[i][j] += input[ii][jj] * kernel[m][n];
                    }
                }
            }
        }
    }
}
```

##  矩阵转置

矩阵转置是指矩阵的行列互换，记作$A^T$，定义为：
<p>
        \[
        A^T = [a_{ij}]_{m\times n} = \left[
        \begin{array}{cccc}
        a_{11} & a_{12} & \cdots & a_{1n} \\
        a_{21} & a_{22} & \cdots & a_{2n} \\
        \vdots & \vdots & \ddots & \vdots \\
        a_{m1} & a_{m2} & \cdots & a_{mn}
        \end{array}
        \right]
        \]
    </p>

即为：
对每一个i，j，将矩阵A的第i行的元素与第j列的元素进行交换。

### 示例代码（C语言）

```c
// 假设我们有一个矩阵 A，我们需要计算它的转置矩阵。
void transpose(int m, int n, double A[m][n], double AT[n][m]) {
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            AT[j][i] = A[i][j];
        }
    }
}
```

### 原地转置

```c
void transpose(int m, int n, double A[m][n]) {
    for (int i = 0; i < m; i++) {
        for (int j = i + 1; j < n; j++) {
            double temp = A[i][j];
            A[i][j] = A[j][i];
            A[j][i] = temp;
        }
    }
}
```

