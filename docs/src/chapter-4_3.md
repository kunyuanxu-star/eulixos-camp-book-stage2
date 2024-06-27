# 矩阵转置

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

## 示例代码（C语言）

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

## 原地转置

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