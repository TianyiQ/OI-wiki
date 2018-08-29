## 二分搜索

二分搜索，也称折半搜索，是用来在一个有序数组中查找某一元素的算法。

以在一个升序数组中查找一个数为例。

它每次考察数组当前部分的中间元素，如果中间元素刚好是要找的，就结束搜索过程；如果中间元素小于所查找的值，那么左侧的只会更小，不会有所查找的元素，只需要到右侧去找就好了；如果中间元素大于所查找的值，同理，右侧的只会更大而不会有所查找的元素，所以只需要到左侧去找。

在二分搜索过程中，每次都把查询的区间减半，因此对于一个长度为 $n$ 的数组，至多会进行 $\mathcal {O}(\log n)$ 次查找。

```c++
int binary_search(int start, int end, int key) {
  int ret = -1;       // 未搜索到数据返回-1下标  
	int mid;
	while (start <= end) {
		mid = start + (end - start) / 2; //直接平均可能會溢位，所以用此算法
		if (arr[mid] < key)
			start = mid + 1;
		else if (arr[mid] > key)
			end = mid - 1;
		else {            // 最後檢測相等是因為多數搜尋狀況不是大於要不就小於
			ret = mid;  
      break;
    }
	}
	return ret;     // 单一出口
}
```

注意，这里的有序是广义的有序，如果一个数组中的左侧或者右侧都满足某一种条件，而另一侧都不满足这种条件，也可以看作是一种有序（如果把满足条件看做 $1$，不满足看做 $0$，至少对于这个条件的这一维度是有序的）。换言之，二分搜索法可以用来查找满足某种条件的最大（最小）的值。

如果我们要求满足某种条件的最大值的最小可能情况（最大值最小化）呢？首先的想法是从小到大枚举这个作为答案的「最大值」，然后去判断是否合法。要是这个答案是单调的就好了，那样就可以使用二分搜索法来更快地找到答案。

要想使用二分搜索法来解这种「最大值最小化」的题目，需要满足以下三个条件：

1.  答案在一个固定区间内；
2.  可能查找一个符合条件的值不是很容易，但是要求能比较容易地判断某个值是否是符合条件的；
3.  可行解对于区间满足一定的单调性。换言之，如果 $x$ 是符合条件的，那么有 $x + 1$ 或者 $x - 1$ 也符合条件。（这样下来就满足了上面提到的单调性）

当然，最小值最大化是同理的。

二分法把一个寻找极值的问题转化成一个判定的问题（用二分搜索来找这个极值）。类比枚举法，我们当时是枚举答案的可能情况，现在由于单调性，我们不再需要一个个枚举，利用二分的思路，就可以用更优的方法解决「最大值最小」、「最小值最大」。这种解法也成为是「二分答案」，常见于解题报告中。

## 三分法

```c++
mid = (left + right) / 2;
midmid = (mid + right) / 2; // 对右侧区间取半
if (cal(mid) > cal(midmid))
  right = midmid;
else
  left = mid;
```

三分法可以用来查找凸函数的最大（小）值。

画一下图好理解一些（图待补）

- 如果 `mid` 和 `midmid` 在最大（小）值的同一侧：
那么由于单调性，一定是二者中较大（小）的那个离最值近一些，较远的那个点对应的区间不可能包含最值，所以可以舍弃。

- 如果在两侧：
由于最值在二者中间，我们舍弃两侧的一个区间后，也不会影响最值，所以可以舍弃。

## 分数规划

分数规划是这样一类问题，每个物品有两个代价 $c_i$，$d_i$，要求通过某种方式选出若干个，使得 $\frac{\sum{c_i}}{\sum{d_i}}$ 最大或最小。

经典的例子是 最优比率环、最优比率生成树 等等。

### 二分法

比如说我们要求的是最小的，记 $L$ 为最优的答案，对这个式子做一些变换：

$$L \geq \frac{\sum{c_i}}{\sum{d_i}}$$

把分母乘过去，把右侧化为 $0$：

$${\sum{d_i}} \times L - {\sum{c_i}} \geq 0$$

即：

$${\sum_{i=1}^N{d_i}} \times L - {\sum_{i=1}^N{c_i}} \geq 0$$

$$\sum_{i=1}^N{d_i \times L - c_i} \geq 0$$

不难发现，如果 $L'$ 比 $L$ 要小，上式左端的值会更大一些。

所以要求得最小的 $L$，我们要求的就变成了让上式左端最接近 $0$ 的 $L$。

不难发现左端的式子是随 $L$ 变化而单调变化的，所以可以通过二分法来解决。

### Dinkelbach 算法

Dinkelbach 算法是每次用上一轮的答案当做新的 $L$ 来输入，不断地迭代，直至答案收敛。