快速排序：

1. 基本

```cpp
void quickSort(int a[], int l, int r) {
    if(l >= r) return ;
    int pivot = a[l];
    int i = l, j = r;
    while(i < j) {
        while(i < j && pivot <= a[j]) j--;
        while(i < j && a[i] <= pivot) i++;
        if(i < j) swap(a[i], a[j]);
    }
    swap(a[i], a[l]);
    quickSort(a, l, i - 1);
    quickSort(a, i + 1, r);
}
```

2. 变体

```cpp
int partition(int l, int r) {
    swap(a[l], a[l + read() % (r - l + 1)]); // 随机交换
    int pivot = a[l], mi = l;
    for(int k = l + 1; k <= r; k++) // 自左向右考察每个[k]
        if(s[k] < pivot)            // 若[k]小于轴点，则将其
            swap(a[++mi], a[k]);    // 与[mi]交换，L向右扩展
    swap(a[l], a[mi]);    // 候选轴点归位
    return mi;    // 返回轴点的秩
}
```


