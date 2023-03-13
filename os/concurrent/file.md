# 并发编程

## 基于进程

```c
fork();
exec();
waitpid();
```

注意事项：子进程是父进程的拷贝，因此要把相应的资源关闭（如文件描述符）

特点：

- 模型清晰：共享文件表、不共享用户空间

- 进程控制和IPC使开销很大

## 基于多路复用

```c
#include <sys/select.h>
int select(int n, fd_set *fdset, NULL, NULL, NULL); // n为最大fd,返回数量
FD_ZERO(fd_set *fdset);
FD_CLR(int fd, fd_set *fdset);
FD_SET(int fd, fd_set *fdset);
FD_ISSET(int fd, fd_set *fdset);
```

> 事件驱动模型：维持状态机，事件是输入，引起状态的改变。

特点：

- 更多控制权

- 单进程/线程顺序执行，阻塞等待事件

- 性能较好

- 编码复杂

- 未利用多核处理器

## 基于线程

```c
#inlucde <pthread.h>
typedef void *(func)(void *);

int pthread_create(pthread_t *tid, pthread_attr_t *attr,
                     func *f, void *arg); // 成功返回0
pthread_t pthread_self(void); // 获取自己的线程id
void pthread_exit(void *thread_return); // 等待对等线程终止并终止本线程
int pthread_canacel(pthread_t tid); // 终止当前线程
int pthread_join(pthread_t tid, void **pthread_return);; // 等待线程终止
int pthread_detach(pthread_t tid); // 分离本线程

pthread_once_t once_control = PTHREAD_ONCE_INIT;
int pthread_once(pthread_once_t *once_control,
                 void (*init_routine)(void));
```

> 基于进程的服务器中两个位置都要关闭文件描述符，但是线程里只需要一次关闭，为什么？

## 多线程的共享变量

- 全局变量：共享

- 本地静态变量：共享

- 本地自动变量：本地所属，但是可以用指针访问到

## 信号量同步线程

### 进程图：

![](/home/whisper/.config/marktext/images/2023-03-13-17-01-32-image.png)

### 信号量

- **P(s)**：如果s非零，那么s减一并立即返回（原子的），否则挂起进进程直到s非零，V操作会唤醒这个进程

- **V(s)**：s加一（原子的），如果有线程阻塞在P操作，唤醒其中的一个

```c
#include <semaphore.h>

int sem_init(sem_t *sem, 0, unsigned int value);
int sem_wait(sem_t *s); // P
int sem_post(sem_t *s); // V
```

> 信号量的提出者Edsger Dijkstra(1930 — 2002) 出生于荷兰 。 名字 P 和 V 来源 于荷兰语单词 Proberen（ 测试 ） 和 Verhogen( 增 加 ）。

### 二元信号量实现互斥

```c
P(s);
todo();
V(s);
```

### 生产者消费者问题

![](/home/whisper/.config/marktext/images/2023-03-13-17-15-36-image.png)

1. 缓冲区的访问互斥

2. 满时不能生产

3. 空时不能消费

```c
typedef struct {
    int *buf;         // Buffer array
    int n;            // Maximum number of slots
    int front;        // buf[(front+1)%n] is first item
    int rear;         // buf[rear&n] is last item
    sem_t mutex;      // Protexts axxesses to buf
    sem_t slots;      // Counts available slots
    sem_t items;      // Counts available items
} sbuf_t;


void sbuf_init(sbuf *sp, int n) {
    sp->buf = Calloc(n, sizeof(int));
    sp->n = n;
    sp->front = sp->rear = 0;
    sem_init(&sp->mutex, 0, 1);
    sem_init(&sp->slots, 0, n);
    sem_init(&sp->items, 0, 0);
}

void sbuf_deinit(sbuf_t *sp) {
    free(sp->buf);
}

void sbuf_insert(sbuf_t *sp, int item) {
    P(&sp->slots);
    P(&sp->mutex);
    sp->buf[(++sp->rear)%(sp->n)] = item;
    V(&sp->mutex);
    V(&sp->items);
}

int sbuf_remove(sbuf_t *sp) {
    int item;
    P(&sp->items);
    P(&sp->mutex);
    item = sp->buf[(++sp->front)%(sp->n)];
    V(&sp->mutex);
    V(&sp->slots);
}
```

### 读者写者问题

读者优先的读写问题的解答：

```c
int readcnt;
sem_t murex, w;
void reader(void) {
    while(1) {
        P(&mutex);
        readcnt++;
        if(readcnt == 1)
            P(&w);
        V(&mutex);
        // READING
        P(&mutex);
        readcnt--;
        if(readcnt == 0)
            V(&w);
        V(&mutex);
    }
}
void writer(void) {
    while(1) {
        P(&w);
        // WRITING
        V(&w);
    }
}
```



## 并发问题

### 线程安全

线程不安全的函数类：

1. 不保护共享变量

2. 保持跨越多个调用的状态的函数

3. 返回指向静态变量的指针的函数

4. 调用线程不安全函数的函数

### 可重入性

> 当它们被多个线程调用时，不会引用任何共享数据

可重入函数通常要比不可重入的线程安全的函数高效一些，因为它们不需要同步操
作。

### 不安全的库函数

![](/home/whisper/.config/marktext/images/2023-03-13-17-47-43-image.png)

### 死锁

![](/home/whisper/.config/marktext/images/2023-03-13-17-48-18-image.png)

参考：csapp


