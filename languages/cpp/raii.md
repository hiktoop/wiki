# RAII

**使用局部对象自动销毁来控制资源的生命周期**

1. 设计一个类封装资源

2. 在构造函数中初始化

3. 在析构函数中执行销毁操作

4. 使用时声明一个该对象的类

例：

```cpp
#include <pthread.h>
#include <cstdlib>
#include <stdio.h>

class Mutex {
 public:
  Mutex();
  ~Mutex();

  void Lock();
  void Unlock(); 

 private:
  pthread_mutex_t mu_;

  // No copying
  Mutex(const Mutex&);
  void operator=(const Mutex&);
};


static void PthreadCall(const char* label, int result) {
  if (result != 0) {
    fprintf(stderr, "pthread %s: %s\n", label, strerror(result));
  }
}

Mutex::Mutex() { PthreadCall("init mutex", pthread_mutex_init(&mu_, NULL)); }

Mutex::~Mutex() { PthreadCall("destroy mutex", pthread_mutex_destroy(&mu_)); }

void Mutex::Lock() { PthreadCall("lock", pthread_mutex_lock(&mu_)); }

void Mutex::Unlock() { PthreadCall("unlock", pthread_mutex_unlock(&mu_)); }
```

```cpp
#include "mutexlock.hpp"
#include <unistd.h>
#include <iostream>

#define    NUM_THREADS     10000

int num=0;
Mutex mutex;

void *count(void *args) {
    MutexLock lock(&mutex);
    num++;
}

int main() {
    int t;
    pthread_t thread[NUM_THREADS];

    for( t = 0; t < NUM_THREADS; t++) {   
        int ret = pthread_create(&thread[t], NULL, count, NULL);
        if(ret) {   
            return -1;
        }   
    }

    for( t = 0; t < NUM_THREADS; t++)
        pthread_join(thread[t], NULL);
    std::cout << num << std::endl;
    return 0;
}
```

> RAII can be summarized as follows:
> 
> - encapsulate each resource into a class, where
>   
>   - the constructor acquires the resource and establishes all class invariants or throws an exception if that cannot be done,
>   
>   - the destructor releases the resource and never throws exceptions;
> 
> - always use the resource via an instance of a RAII-class that either
>   
>   - has automatic storage duration or temporary lifetime itself, or
>   
>   - has lifetime that is bounded by the lifetime of an automatic or temporary object
>   
>   [from cppreference.com](https://en.cppreference.com/w/cpp/language/raii) 

> 参考：
> 
> - https://zhuanlan.zhihu.com/p/34660259
> 
> - https://en.cppreference.com/w/cpp/language/raii


