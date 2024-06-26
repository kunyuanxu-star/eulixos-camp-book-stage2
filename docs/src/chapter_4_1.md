# FIFO 页面置换算法

## 原理
FIFO（First-In-First-Out）页面置换算法是一种最简单的页面置换算法。它将最早加载进内存的页面置换出去。换句话说，当需要一个新的页面时，最先进入内存的页面将被移除。

## 函数实现
以下是FIFO页面置换算法的实现：

```c
#include <stdio.h>
#include <string.h>

#define MAX_FRAMES 10

void fifo_page_replacement(char *queue_frames, int num_frames) {
    int frames[MAX_FRAMES];
    int front = 0;
    int rear = 0;
    int size = 0;
    int page_faults = 0;

    for (int i = 0; i < num_frames; i++) {
        frames[i] = -1;  // 初始化页框为空
    }

    for (int i = 0; i < strlen(queue_frames); i++) {
        int page = queue_frames[i] - '0';
        int found = 0;

        // 检查页面是否已经在页框中
        for (int j = 0; j < size; j++) {
            if (frames[j] == page) {
                found = 1;
                break;
            }
        }

        // 页面不在页框中，需要置换
        if (!found) {
            page_faults++;
            frames[rear] = page;
            rear = (rear + 1) % num_frames;

            // 如果页框未满，增加当前大小
            if (size < num_frames) {
                size++;
            }
        }

        // 打印当前页框状态
        printf("Page %d: [", page);
        for (int j = 0; j < size; j++) {
            printf("%d", frames[j]);
            if (j < size - 1) {
                printf(" ");
            }
        }
        printf("]\n");
    }

    printf("Total page faults: %d\n", page_faults);
}

int main() {
    char queue_frames[] = "70120304230321201701";
    int num_frames = 3;

    fifo_page_replacement(queue_frames, num_frames);

    return 0;
}
```