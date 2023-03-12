# 算法竞赛进阶指南笔记

---

## 0x0 基本算法

---

### 位运算

#### memset 赋值 0x3f ：

`memset(a, val, sizeof(a))` 初始化int数组，该语句把 `val(0x00~0xff)` 填充到数组 `a` 的每个字节上，而 `int` 占四个字节，故 `memset` 只能赋值出 **每八位相同** 的 `int` 。

`0x7f 7f 7f 7f` 是 `memset` 语句能初始化出的最大数值，而 `0x3f 3f 3f 3f` 的两倍不超过前者，因此为了避免加法运算上溢或者繁琐的判断，我们常用 `0x3f` 来赋值初始化数组。

#### c++ 右移

默认算术右移， **右移向下取整，/ 2 向 0 取整** ，例如：

```c
3 >> 1 = 1; (-3)>>1 = -2;
3 / 2 = 1; (-3) / 2 = -1;
```

#### 快速幂算法

迭代格式：

```c
int power(int a, int n) {
    int ans = 1;
    for( ; n; n >>= 1) {
        if(n & 1) ans *= a;
        a *= a;
    }
    return ans;
}
```

递归格式：

```c
int power(int a, int n) {
    if(n == 0) return 1;
    if(n & 1) return power(a, n - 1) * a;
    int tmp = power(a, n >> 1);
    return tmp * tmp;
}
```

在我第一次学习的时候，我觉得递归格式比迭代直观的多，原因在于我知道递归格式干了什么，但是当我理解了迭代的原理后就觉得迭代更直观了，每步都对应了每一位的运算，反而是递归格式，好像在绕弯，其实把数学式学出来就很好理解了，两者的原理虽然是相同的，但是迭代比递归更加本源。

#### 运算符优先级

![](C:\Users\86151\AppData\Roaming\marktext\images\2023-01-14-13-04-24-image.png)

#### 成对变换

```c
n xor 1 == n + 1; // n 为偶数
n xor 1 == n - 1; // n 为奇数
```

故 **0 与 1、2 与 3、4 与 5** ··· 关于 `xor 1` 构成 **成对变换** ，这一性质经常用于图论邻接表中边集的存储。在具有无向边（双向边）的图中把一对正反方向的边分别存储在邻接表数组的第 n 与 n + 1位置（其中 n 是偶数），就可以通过 `xor 1` 的运算获取当前边的反向边的存储位置。

#### lowbit 运算

```c
#define getlowbit(n) (n & (-n))
#define eraselowbit(n) (n & (n - 1))
```

#### 一条未解的数学规律

$$
\forall k \in [0, 35], 2^k mod 37互不相等
$$

---

### 递推与递归

#### 定义

> 对于一个待求解的问题，当它局限在某边界、某个小范围或者某种特殊情况下时，其答案往往是已知的。如果能够将该解答的应用场景扩大到原问题的状态空间，并且扩展过程的每一步具有相似性，就可以考虑使用递推和递归求解。

> 以已知的 “问题边界” 为起点向 “原问题” 正向推导的扩展方式就是递推。然而在很多时候，，推导的路线难以确定，这时以 “原问题” 为起点尝试寻找把状态空间缩小到已知的 “问题边界” 的路线，再通过该路线反向回溯的遍历方式就是递归。

#### 等比数列求和

$$
\begin{aligned}
& sum(p,c) = 1 + p + p^2 + ... + p^c = \ ? \\
& c 为奇数时: \\
& sum(p,c) = (1 + p + ... + p^ \frac{c-1}{2}) + (p^ \frac{c+1}{2} + ... + p^c) \\
&\qquad\qquad = (1 + p + ... + p^ \frac{c-1}{2}) + p^ \frac{c+1}{2} * (1 + p + ... + p^ \frac{c-1}{2}) \\
&\qquad\qquad = (1 + p^ \frac{c+1}{2}) * sum(p, \frac{c-1}{2}) \\
& c为偶数时，类似的: \\
& sum(p,c) = (1 + p^ \frac{c}{2}) * sum(p, \frac{c}{2} - 1) + p^c
\end{aligned}
$$

每次分治，问题规模均会缩小一半，配合快速幂即可在 $O(log\,c)$ 的时间内求出等比数列的和。

#### 非递归实现函数调用的例子

> 非递归实现组合型枚举（从 1~n 这 n 个整数中随机选出 $m(0 \le m \le n < 20)$ 个，输出所有可能的选择方案）：

```c
vector<int> chosen;
int stack[100010], top = 0, address = 0, m, n;
void call(int x, int ret_addr) { // 模拟计算机汇编指令call
    int old_top = top;
    stack[++top] = x; // 参数x
    stack[++top] = ret_addr; // 返回地址标号
    stack[++top] = old_top; // 在栈顶记录以前的top值
}
int ret() { // 模拟计算机汇编指令ret
    int ret_addr = stack[top-1];
    top = stack[top]; // 恢复以前的top值
    return ret_addr;
}
int main() {
    cin >> n >> m;
    call(1, 0); // 相当于 call(1)，返回后 address = 0
    while(top) {
        int x = stack[top - 2]; // 获取参数
        switch(address) {
            case 0:
                if(chosen.size() > m || chosen.size() + (n - x + 1) < m) {
                    address = ret(); // return
                    continue;
                }
                if(x == n + 1) {
                    for(int i = 0; i < chosen.size(); i++)
                        printf("%d", chosen[i]);
                    puts("");
                    address = ret(); // return
                    continue;
                }
                call(x + 1, 1); // 相当与 call(x+1)，返回后从 case1 继续
                address = 0;
                continue; // 回到 while 循环开头，相当于新的递归
            case 1:
                chosen.push_back(x);
                call(x + 1, 2);
                address = 0;
                continue;
            case 2:
                chosen.pop_back();
                address = ret(); // 相当于原函数结尾，执行return
        }
    }
}
```

> 非递归汉诺塔（取自[蒋炎岩的操作系统课程](http://jyywiki.cn/pages/OS/2022/demos/hanoi-nr.c) ）：

```c
typedef struct {
  int pc, n;
  char from, to, via;
} Frame;

#define call(...) ({ *(++top) = (Frame) { .pc = 0, __VA_ARGS__ }; })
#define ret()     ({ top--; })
#define goto(loc) ({ f->pc = (loc) - 1; })

void hanoi(int n, char from, char to, char via) {
  Frame stk[64], *top = stk - 1;
  call(n, from, to, via);
  for (Frame *f; (f = top) >= stk; f->pc++) {
    switch (f->pc) {
      case 0: if (f->n == 1) { printf("%c -> %c\n", f->from, f->to); goto(4); } break;
      case 1: call(f->n - 1, f->from, f->via, f->to);   break;
      case 2: call(       1, f->from, f->to,  f->via);  break;
      case 3: call(f->n - 1, f->via,  f->to,  f->from); break;
      case 4: ret();                                    break;
      default: assert(0);
    }
  }
}
```

---

### 前缀和与差分

#### 区间操作转化为差分序列的单点操作

把序列 $A$ 的 `[l,r]` 区间加上 $d$ ，相当于其差分序列 $B_l$ 加上 $d$ ，$B_{r+1}$ 减去 $d$。

---

### 二分

#### 二分写法的两种形式：

- 缩小范围时，$r = mid,\ l = mid + 1$ ，取中间值时，$mid = (l + r) / 1$ 。

- 缩小范围时，$l = mid, \ r = mid - 1$，取中间值时，$mid = (l + r + 1) / 2$

分别对应下取整和上取整，避免最小区间 `[i, i + 1]` 时陷入死循环。

#### 三分求单峰函数极值

严格单峰函数 $f$ 的定义域 $[l,r]$ 上任取两点 $lmid \,与 \, rmid$ :

1. 若 $f(lmid) = f(rmid)$ ，则 $l = lmid, \ r = rmid$ .

2. 若 $f(lmid) < f(rmid)$ ，则 $l = lmid$ .

3. 若 $f(lmid) > f(rmid)$ ，则 $r = rmid$ .

> 取 $lmid与rmid$ 为三等分点，则定义域范围没次缩小 $1/3$ 。
> 
> 取 $lmid$ 与$rmid$ 为二等分点两侧近处，则定义域范围每次缩小 $1/2$  。

---

### 排序

#### 离散化

```c
void discrete() {
    sort(a + 1, a + n + 1);
    for(int i = 1; i <= n; i++) // 也可用STL中的unique函数
        if(i == 1 || a[i] != a[i-1])
            b[++m] = a[i];
}
int query(int x) { // 查询x映射为哪个1~m之间的整数
    return lower_bound(b + 1, b + m + 1, x) - b;
}
void discrete2() { // 全部获取、另一种版本
    // std::vector<int> a, b; // b 是 a 的一个副本
    sort(a.begin(), a.end());
    a.erase(unique(a.begin(), a.end()), a.end());
    for (int i = 0; i < n; ++i)
        b[i] = lower_bound(a.begin(), a.end(), b[i]) - a.begin();
}
```

#### 平均数技巧

> 要使平均数满足某个条件，可以让区间的所有数减去这个条件，使这个条件变为0，从而简化计算。

#### 解题感悟

> 先观察题目的已有变量，结合状态机变化过程归纳出一些性质

---

### 倍增

#### 倍增模板

```c
int func(int s) {
    int p = 1, k = s, sum = 0;
    while(p) { // p == 0 即为不再能增加
        int val = s[k + p] - s[k];
        if(sum + val <= T) { // 倍增
            sum += val;
            k += p;
            p <<= 1;
        }
        else p >>= 1; // 倍减
    }
    return k - s;
}
```

#### ST 算法

> **可重复贡献问题** 是指对于运算 $opt$，满足 $x\ opt\ x = x$，则对应的区间询问就是一个可重复贡献问题。例如，最大值有 $max(x, x) = x$，gcd 有 $gcd(x,x) = x$，所以 RMQ 和区间 GCD 就是一个可重复贡献问题。像区间和就不具有这个性质，如果求区间和的时候采用的预处理区间重叠了，则会导致重叠部分被计算两次，这是我们所不愿意看到的。另外，![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7 "\operatorname{opt}") 还必须满足结合律才能使用 ST 表求解。

对于给定的 $A$ 数列，$st$ 算法能在 $O(N\,logN)$ 时间的预处理后以 $O(1)$ 时间复杂度在线回答 $A[l,r]_{opt}$ .

##### 模板

```c
void getLog(int n) { // 预处理生成log2的值
    int log[n];
    log[1] = 0;
    for(int i = 2; i <= n; i++) log[i] = log[i>>1] + 1;
}
void ST_prework() { // f[i][j]表示从i到i+2^j区间的值（左开右闭）
    for(int i = 1; i  <= n; i++) f[i][0] = a[i];
    for(int j = 1; j <= log(n); j++)
        for(int i = 1; i + (1<<j) - 1 <= n; i++)
            f[i][j] = max(f[i][j-1], f[i+(1<<(j-1))][j-1]);
}
int ST_query(int l, int r) {
    int k = log(r - l + 1); // k 是向下取整的
    return max(f[l][k], f[r-(1<<k)+1][k]);
}
```

---

### 贪心

#### 贪心问题常见证明手段

1. 微扰（邻项交换）

        证明在任意局面下，任何对局部最优策略的微小改变都会造成整体结果变差。经常用于以 “排序” 为贪心策略的证明。

2. 范围缩放

        证明任何对局部最优策略作用范围的扩展都不会造成整体结果变差。

3. 决策包容性

        证明在任意局面下，作出局部最优决策以后，在问题状态空间中的可达集合包含了作出其他任何决策后的可达集合。换言之，这个局部最优策略提供的可能性包含其他所有策略提供的可能性。

4. 反证法

5. 数学归纳法

---

### 总结归纳

任何问题都有其涉及的范围，称之为问题的 **状态空间** 。求解一个问题，就是在这个状态空间里的遍历和映射。递推与递归是遍历状态空间的两种基本形式。各种算法对于 **遍历顺序** 、**遍历过程中的决策方法** 、**状态空间中各状态之间的映射方式** 给出指导。枚举与模拟是按照问题的直接表述形式对状态空间进行朴素的遍历，搜索则是带有一定的选择性、决策性的遍历，贪心是在每步决策时采取局部最优策略的遍历，动态规划则是基于全局考量的分阶段、等价、代表、拼接等手段，直接降低遍历时需要面对的时空规模。

---

## 0x1 基本数据结构

### 栈

#### 进出栈序列问题

解决方法：

1. 搜索

2. 递推

        考虑 “1” 这个数排在最终出栈序列中的位置为 k ：

            1. 整数 1 入栈

            2. 整数 2 ~ k 这 k - 1 个数按某顺序进出栈

            3. 整数 1 出栈

            4. 整数 k + 1 ~ N 这 N - k 个数按某顺序进出栈

$$
S_N = \sum_{k = 1}^N S_{k-1} * S_{N-k}
$$

3. 动态规划

        $F[i,j]$ 表示有 i 个数尚未进栈，j个数在栈里。

$$
F[i,j] = F[i-1,j+1] + F[i,j-1]
$$

4. Catalan数

$$
\frac{C_{2N}^N} {N+1}
$$

#### 中缀表达式转后缀表达式

1. 建立一个用于存运算的栈，逐一扫描该中缀表达式中的元素。
   
   1. 遇到数，输出该数。
   
   2. 遇到左括号，把左括号入栈。
   
   3. 遇到右括号，不断输出栈顶，直到遇到左括号，最后弹出左括号。
   
   4. 遇到运算符，只要栈顶符号优先级不低于新符号就不断出栈输出，最后把新符号入栈。

2. 依次取出并输出栈中剩余符号。 

##### 单调栈

![](C:\Users\86151\AppData\Roaming\marktext\images\2023-01-15-14-13-30-image.png)

```c
a[n + 1] = p = 0; // 多存一个0使栈中所有矩形被弹出
for(int i = 1; i <= n + 1; i++) {
    if(a[i] > s[p]) // 当前矩形比栈顶高
        s[++p] = a[i], w[p] = 1; // 入栈
    else { // 当前矩形比栈顶低
        int width = 0;
        while(p && s[p] > a[i]) { // 弹出比当前矩形高的矩形
            width +=  w[p];
            ans = max(ans, (long long)width * s[p]); // 栈尾（当前矩形的前一个矩形）到p的矩形面积
            p--;
        }
        s[++p] = a[i], w[p] = width + 1; // 当前矩形添加到栈顶，前面矩形宽度加到当前矩形上，相当于删去之前矩形高过当前矩形的部分
    }
}
```

---

### 队列

#### 单调队列

> 最大子序和

```c
int l = 1, r = 1;
q[1] = 0;
for(int i = 1; i <= n; i++) {
    while(l <= r && q[l] < i - m) l++; // 超出范围时弹出队头
    ans = max(ans, sum[i] - sum[q[l]]); // 当前区间尾减去最小的前缀
    while(l <= r && sum[q[r]] >= sum[i]) r--; // 队列保持单调递增以存储最优最小值
    q[++r] = i; // 入队
}
```

---

### 链表与邻接表

#### 数组模拟链表模板

```c
struct Node {
    int value;
    int prev, next;
} node[SIZE];
int head, tail, tot;
int initialize() {
    tot = 2;
    head = 1, tail = 2;
    node[head].next = tail;
    node[tail].prev = head;
}
int insert(int p, int val) {
    int q = ++tot;
    node[q].value = val;
    node[node[p].next].prev = q;
    node[q].next = node[p].next;
    node[p].next = q;
    node[q].prev = p;
    return q;
}
void remove(int p) {
    node[node[p].prev].next = node[p].next;
    node[node[p].next].prev = node[p].prev;
}
void clear() {
    memset(node, 0, sizeof node);
    head = tail = tot = 0;
}
```

#### 邻接表

![](C:\Users\86151\AppData\Roaming\marktext\images\2023-01-15-16-09-46-image.png)

图结构

![](C:\Users\86151\AppData\Roaming\marktext\images\2023-01-15-16-12-26-image.png)

```c
void add(int x, int y, int w) {
    ver[++tot] = y, edge[tot] = w; // 数据
    next[tot] = head[x], head[x] = tot; // 在表头x处插入
}
for(int i = head[x]; i; i = next[i]) {
    int y = ver[i],w = edge[i]; // x->y，权值为w
}
```

> 结合 $0x10$ 所讲 “成对变换” ，初始化 `tot = 1` 使无向边存储在 `2 和 3` 、 `4 和 5` 、 `6 和 7` ··· 的位置上。`ver[i]` 作为边的终点，通过 `ver[i ^ 1]` 取边的起点。

---

### hash

#### 字符串hash

> 字符的所有可能数 n 作为进制的单位（ n 进制），把字符串转换为相应的数字。

> $O(n)$ 时间预处理字符串所有前缀 $hash$ 值，并在 $O(1)$ 时间内查询它的任意子串的 $hash$ 值。

---

### 字符串

#### KMP 算法

```c
// 求next数组
next[1] = 0;
for(int i = 2, j = 0; i <= n; i++) {
    while(j > 0 && a[i] != a[j+1]) j = next[j];
    if(a[i] == a[j+1]) j++;
    next[i] = j;
}
// 求f数组
for(int i = 1, j = 0; i <= m; i++) {
    while(j > 0 && (j == n || b[i] != a[j+1])) j = next[j];
    if(b[i] == a[j+1]) j++;
    f[i] = j;
    // if(f[i] == n) 此时就是A在B中的某次出现
}
```

---

### Trie

> 实现字符串快速检索的多叉树结构

```c
int trie[SIZE][26], tot = 1; // 初始化，每个字符串由小写字母构成
void insert(char *srt) { // 插入一个字符串
    int len = strlen(str), p = 1;
    for(int k = 0; k < len; k++) {
        int ch = str[k] - 'a';
        if(trie[p][ch] == 0) trie[p][ch] = ++tot;
        p = trie[p][ch];
    }
    end[p] = true;
}
bool search(char *str) { // 检索字符串是否存在
    int len = strlen(str), p = 1;
    for(int k = 0; k < len; k++) {
        p = trie[p][str[k] - 'a'];
        if(p == 0) return false;
    }
    return end[p];
}
```

---

### 二叉堆

#### 模板

```c
int heap[SIZE], n; // 大根堆
void up(int p) {
    while(p > 1) {
        if(heap[p] > heap[p>>1]) {
            swap(heap[p], heap[p>>1]);
            p >>= 1;
        }
        else break;
    }
}
void down(int p) {
    int s = p << 1;
    while(s <= n) {
        if(s < n && heap[s] < heap[s+1]) s++;
        if(heap[s] > heap[p]) {
            swap(heap[s], heap[p]);
            p = s, s = p << 1;
        }
        else break;
    }
}
void insert(int val) { // 插入元素
    heap[++n] = val;
    up(n);
}
void extract() { // 删除堆顶
    heap[1] = heap[n--];
    down(1);
}
void remove(int k) { // 删除k节点
    heap[k] = heap[n--];
    up(k), down(k);
}
int gettop() { // 获取堆顶
    return heap[1];
}
```

#### Huffman Tree

##### 树的带权路径长度：

> 设 k 叉树具有 n 个带权叶结点，从根结点到各叶结点的路径长度与相应叶节点权值的乘积之和称为 **树的带权路径长度（Weighted Path Length of Tree，WPL）**。
> 
> $$
> WPL = \sum_{i=1}^n w_i l_i
> $$

WPL 最小的树称为哈夫曼树（Huffman Tree）。

##### 哈夫曼树的性质

1. WPL最小

2. 所有节点都是叶节点

---

## 0x2 搜索

### 树与图的遍历

---

### 深度优先搜索

---

### 枝剪

#### 常用剪枝方法

1. 优化搜索顺序

        尽量减少上层的分支数

2. 排除等效冗余

        当能判断从搜索树的当前节点上沿着某几条不同的分支到达的子树是等效的，就只对其中一条分支执行搜索。

3. 可行性剪枝

        当前状态无法到达递归边界时回溯。

4. 最优性枝剪

        到当前节点的代价已经超过了最优解时回溯。

5. 记忆化

        记录每个状态的搜索结果，当重复遍历一个状态时直接检索返回。

---

### 迭代加深

---

### 广度优先搜索

---

### 广搜变形

---

### A*

---

### IDA*

---

## 0x3 数学知识

---

## 0x4 数据结构进阶

---

## 0x5 动态规划

---

## 0x6 图论
