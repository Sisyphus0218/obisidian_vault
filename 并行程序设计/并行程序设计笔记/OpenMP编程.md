# Shared Memory Programming: OpenMP
## Shared memory parallelism with threads

编程模型：共享内存
程序：线程集合
线程有私有变量和共享变量

POSIX Threads
POSIX：Portable Operating System Interface 可移植操作系统接口
Pthreads：POSIX 的线程标准，定义了创建和操纵线程的一套 API

```
#include <pthread.h>

int pthread_create( pthread_t *thread, 
					const pthread_attr_t *attr, 
					void *(*start_routine)(void*),
					void *arg);
```

`thread`：指向 pthread_t 类型的指针，用来与线程交互
`attr`：指定线程的一些属性，如栈的大小、调度优先级等，一般默认为 NULL
`void *(*start_routine)(void*)`：线程函数
`arg`：线程函数的参数
注：`void*` 无类型指针，可以向函数传递任意类型的参数，让函数返回任意类型的结果

编译要用：gcc –lpthread

data race
```
static int s= 0
```

## What and why OpenMP?





## Parallel programming with OpenMP





## Introduction to OpenMP

### Creating parallelism

1. Parallel Loops
2. Synchronizing
3. Data sharing



## Beneath the hood

– Shared memory hardware





## Summary