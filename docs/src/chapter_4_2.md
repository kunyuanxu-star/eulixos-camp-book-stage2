# LRU 页面置换算法

## 原理
LRU（Least Recently Used）页面置换算法根据页面最近使用的情况来决定置换哪一个页面。具体来说，它将最久未被使用的页面置换出去。

## 函数实现
以下是LRU页面置换算法的实现：

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_FRAMES 100

/**
 * 函数：模拟LRU页面置换算法。
 *
 * @param queue_frames 一个字符串，表示页面访问序列。
 *                     字符串中的每个字符是一个数字，表示一个页面号。
 * @param num_frames   页框的数量，表示物理内存中可用的页框数。
 */
void lru_page_replacement(char *queue_frames, int num_frames) {
    int frames[MAX_FRAMES];
    int counter[MAX_FRAMES];
    int time = 0;
    int page_faults = 0;

    for (int i = 0; i < num_frames; i++) {
        frames[i] = -1;  // 初始化页框为空
        counter[i] = 0;  // 初始化计数器
    }

    for (int i = 0; i < strlen(queue_frames); i++) {
        int page = queue_frames[i] - '0';
        int found = 0;

        // 检查页面是否已经在页框中
        for (int j = 0; j < num_frames; j++) {
            if (frames[j] == page) {
                found = 1;
                counter[j] = ++time;  // 更新页面使用时间
                break;
            }
        }

        // 页面不在页框中，需要置换
        if (!found) {
            page_faults++;
            int lru_index = 0;
            int lru_time = counter[0];

            // 找到最久未使用的页面
            for (int j = 1; j < num_frames; j++) {
                if (counter[j] < lru_time) {
                    lru_time = counter[j];
                    lru_index = j;
                }
            }

            frames[lru_index] = page;
            counter[lru_index] = ++time;

            // 打印当前页框状态
            printf("Page %d: [", page);
            for (int j = 0; j < num_frames; j++) {
                printf("%d", frames[j]);
                if (j < num_frames - 1) {
                    printf(" ");
                }
            }
            printf("]\n");
        }
    }

    printf("Total page faults: %d\n", page_faults);
}

int main() {
    char queue_frames[] = "70120304230321201701";
    int num_frames = 3;

    lru_page_replacement(queue_frames, num_frames);

    return 0;
}
```

这两个函数分别实现了FIFO和LRU页面置换算法。在每个函数中，`queue_frames`是页面访问序列，`num_frames`是可用的页框数。每次访问一个页面时，函数会检查该页面是否在页框中。如果不在，则需要置换一个页面。FIFO算法总是置换最早进入内存的页面，而LRU算法则置换最久未使用的页面。
