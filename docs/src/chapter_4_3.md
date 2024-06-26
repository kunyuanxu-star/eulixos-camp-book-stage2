# 模拟时间片轮转调度算法

## 原理
时间片轮转（Round Robin, RR）是一种广泛使用的进程调度算法。其核心思想是将CPU时间划分成固定长度的时间片（time slices），并按顺序将这些时间片分配给就绪队列中的每个进程。每个进程在其时间片内运行，当时间片用完时，如果进程尚未完成，则将其置于就绪队列的末尾，等待下一轮调度。这样可以确保所有进程都能公平地获得CPU资源。

## 进程结构体

进程结构体包含以下字段：

- `pid`：进程ID，用于标识进程。
- `arrival_time`：到达时间，进程进入系统的时间。
- `burst_time`：运行时间，进程需要的总CPU时间。
- `remaining_time`：剩余运行时间，用于记录进程还需要多少时间完成。
- `completion_time`：完成时间，进程完成执行的时间。
- `turnaround_time`：周转时间，进程从到达至完成所经历的总时间（完成时间 - 到达时间）。
- `waiting_time`：等待时间，进程在就绪队列中等待的时间（周转时间 - 运行时间）。

## 输入数据

时间片长度：4 个时间单位。  
进程数量：4 个进程。  
每个进程的到达时间和运行时间如下表所示：  

| 进程ID | 到达时间 | 运行时间 |
|--------|----------|----------|
| P1     | 0        | 10       |
| P2     | 1        | 6        |
| P3     | 2        | 8        |
| P4     | 3        | 4        |

## 计算方法

模拟时间片轮转调度算法，并计算每个进程的完成时间、周转时间和等待时间。

```c
#include <stdio.h>

typedef struct {
    int pid;               // 进程ID
    int arrival_time;      // 到达时间
    int burst_time;        // 运行时间
    int remaining_time;    // 剩余运行时间
    int completion_time;   // 完成时间
    int turnaround_time;   // 周转时间
    int waiting_time;      // 等待时间
} Process;

#define TIME_SLICE 4
#define NUM_PROCESSES 4

void calculateTime(Process processes[], int n) {
    int time = 0;
    int completed = 0;

    while (completed < n) {
        for (int i = 0; i < n; i++) {
            if (processes[i].arrival_time <= time && processes[i].remaining_time > 0) {
                if (processes[i].remaining_time <= TIME_SLICE) {
                    time += processes[i].remaining_time;
                    processes[i].remaining_time = 0;
                    processes[i].completion_time = time;
                    processes[i].turnaround_time = processes[i].completion_time - processes[i].arrival_time;
                    processes[i].waiting_time = processes[i].turnaround_time - processes[i].burst_time;
                    completed++;
                } else {
                    time += TIME_SLICE;
                    processes[i].remaining_time -= TIME_SLICE;
                }
            }
        }
    }
}

int main() {
    Process processes[NUM_PROCESSES] = {
        {1, 0, 10, 10, 0, 0, 0},
        {2, 1, 6, 6, 0, 0, 0},
        {3, 2, 8, 8, 0, 0, 0},
        {4, 3, 4, 4, 0, 0, 0}
    };

    calculateTime(processes, NUM_PROCESSES);

    for (int i = 0; i < NUM_PROCESSES; i++) {
        printf("P%d 完成时间: %d, 周转时间: %d, 等待时间: %d\n",
               processes[i].pid,
               processes[i].completion_time,
               processes[i].turnaround_time,
               processes[i].waiting_time);
    }

    return 0;
}
```

## 输出结果
根据输入数据和时间片轮转调度算法的规则，输出结果如下：

```
P1 完成时间: 28, 周转时间: 28, 等待时间: 18
P2 完成时间: 22, 周转时间: 21, 等待时间: 15
P3 完成时间: 26, 周转时间: 24, 等待时间: 16
P4 完成时间: 16, 周转时间: 13, 等待时间: 9
```

### 5.1.6结论
通过时间片轮转调度算法，每个进程都能公平地获得CPU资源，并且算法能够计算出每个进程的完成时间、周转时间和等待时间。这对于实现操作系统中的进程调度是非常重要的。


