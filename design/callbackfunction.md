# callback function

> 回调函数是指一段以参数的形式传递给其它代码的可执行代码。

```c
// 处理
res = request(); // 获取请求
handle(res); // 处理结果
// 回调
request(handle); // 把处理函数交给请求函数
```

同步回调

![](https://pic4.zhimg.com/80/v2-78f8d8e65db57df890c3509e06d2c26f_720w.webp)

异步回调（事件驱动编程）

不足：

```c
// 同步
a = GetServiceA();
b = GetServiceB(a);
c = GetServiceC(b);
d = GetServiceD(c);
// 异步
GetServiceA(function(a){
    GetServiceB(a, function(b){
        GetServiceC(b, function(c){
            GetServiceD(c, function(d) {
                ....
            });
        });
    });
});
```

参考：

https://zhuanlan.zhihu.com/p/326902537
