## [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)（困难）

给定一个数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。

返回滑动窗口中的最大值。

<br/>

**示例:**

```
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

<br/>

**提示：**

- 1 <= nums.length <= 10^5
- -10^4 <= nums[i] <= 10^4
- 1 <= k <= nums.length

<br/>

### 答案：

#### 1，暴力求解

最简单的一种方式就是**暴力求解**，原理其实很简单，就是窗口在往右滑动的过程中，每滑动一步就计算窗口内最大的值，就以上面的数据画个图来看下

![](https://raw.githubusercontent.com/sdwwld/algorithms/master/img/leetcode/0239/640.png)

代码比较简单，直接看下

```java
public int[] maxSlidingWindow(int[] nums, int k) {
    //边界条件判断
    if (nums == null || nums.length == 0)
        return new int[0];
    int res[] = new int[nums.length - k + 1];
    for (int i = 0; i < res.length; i++) {
        int max = nums[i];
        //在每个窗口内找到最大值
        for (int j = 1; j < k; j++) {
            max = Math.max(max, nums[i + j]);
        }
        res[i] = max;
    }
    return res;
}
```

如果看过之前讲的[378，数据结构-7,堆](http://mp.weixin.qq.com/s?__biz=MzU0ODMyNDk0Mw==&mid=2247487143&idx=1&sn=293a762267e087032c8d6a6f5bdd8d13&chksm=fb419d87cc361491db965c4db6e6f3c187fe63e4aa80e03f6baf2716ed4f983b4d687dab747d&scene=21#wechat_redirect)我们还可以使用堆来解决，这里可以使用最大堆，堆顶的元素是最大的，因为这题求的就是窗口内的最大值，堆的大小就是窗口的大小。因为堆的每次删除和添加都会涉及到往下调整和往上调整，所以效率一般不是很高，也可以看下，这里就是用PriorityQueue来代替堆

```java
public int[] maxSlidingWindow(int[] nums, int k) {
    //边界条件的判断
    if (nums == null || k <= 0)
        return new int[0];
    int[] res = new int[nums.length - k + 1];
    int index = 0;
    //优先队列
    PriorityQueue<Integer> queue = new PriorityQueue<>((t1, t2) -> t2 - t1);
    for (int i = 0; i < nums.length; i++) {
        //元素添加到堆中
        queue.add(nums[i]);
        //如果堆的大小大于k，把最先加入的元素给移除
        if (queue.size() > k)
            queue.remove(nums[i - k]);
        if (i >= k - 1) {
            //把堆顶元素加入到数组中
            res[index++] = queue.peek();
        }
    }
    return res;
}
```

<br/>

#### 2，双端队列求解

我们知道一般的队列都是先进先出的，但双端队列两端都可以进出，如果对双端队列不熟悉的可以看下之前写的[359，数据结构-3,队列](http://mp.weixin.qq.com/s?__biz=MzU0ODMyNDk0Mw==&mid=2247486409&idx=2&sn=d6548abcc010f96dd632ba6928afd07e&chksm=fb4198e9cc3611ff0625fb40c3604856368f426c2c4434c78d544e4b5d6d468f220d6be6d2a4&scene=21#wechat_redirect)。

<br/>

使用双端队列首先要搞懂一个问题，就是**在双端队列中，要始终保证队头是队列中最大的值**。那怎么保证呢，就是在添加一个值之前，比他小的都要被移除掉，然后再添加这个值。我们举个例子，比如窗口大小是3，双端队列中依次添加3个值[4,2,5]，在添加5之前我们要把4和2给移除，让队列中只有一个5，因为窗口是往由滑动的，当添加5的时候，4和2都不可能再成为最大值了，并且4和2要比5还先出队列，搞懂了上面的过程我们随便画个图看下

![](https://raw.githubusercontent.com/sdwwld/algorithms/master/img/leetcode/0239/641.png)

搞懂了上面的过程代码就很容易写了，再看代码之前先来看一下双端队列常用的几个函数

![](https://raw.githubusercontent.com/sdwwld/algorithms/master/img/leetcode/0239/642.png)

代码如下

```java
public int[] maxSlidingWindow(int[] nums, int k) {
    //边界条件的判断
    if (nums == null || k <= 0)
        return new int[0];
    int[] res = new int[nums.length - k + 1];
    int index = 0;
    //双端队列，就是两边都可以插入和删除数据的队列，注意这里存储
    //的是元素在数组中的下标，不是元素的值
    Deque<Integer> qeque = new ArrayDeque<>();
    for (int i = 0; i < nums.length; i++) {
        //如果队列中队头元素和当前元素位置相差i-k，相当于队头元素要
        //出窗口了，就把队头元素给移除，注意队列中存储
        //的是元素的下标（函数peekFirst()表示的是获取队头的下标，函数
        //pollFirst()表示的是移除队头元素的下标）
        if (!qeque.isEmpty() && qeque.peekFirst() <= i - k) {
            qeque.pollFirst();
        }
        //在添加一个值之前，前面比他小的都要被移除掉，并且还要保证窗口
        //中队列头部元素永远是队列中最大的
        while (!qeque.isEmpty() && nums[qeque.peekLast()] < nums[i]) {
            qeque.pollLast();
        }
        //当前元素的下标加入到队列的尾部
        qeque.addLast(i);
        //当窗口的长度大于等于k个的时候才开始计算（注意这里的i是从0开始的）
        if (i >= k - 1) {
            //队头元素是队列中最大的，把队列头部的元素加入到数组中
            res[index++] = nums[qeque.peekFirst()];
        }
    }
    return res;
}
```

<br/>

#### 3，两端扫描解决

这个不太容易想到，就是根据窗口大小把数组分成n个窗口，每个窗口分别从左往右和从右往左扫描，记录扫描的最大值，就像下面这样

![](https://raw.githubusercontent.com/sdwwld/algorithms/master/img/leetcode/0239/643.png)

窗口分好之后一个从前往后扫描一个从后往前扫描，记录每个窗口扫描的最大值。我们取窗口内的最大值的时候，如果窗口在原数组中开始的下标正好是k的倍数，比如下面这样，他的最大值很容易找

![](https://raw.githubusercontent.com/sdwwld/algorithms/master/img/leetcode/0239/644.png)

但如果窗口滑动到下面这种情况下

![](https://raw.githubusercontent.com/sdwwld/algorithms/master/img/leetcode/0239/645.png)

如果要找这个窗口的最大值，我们就要选窗口内从左边扫描最后一个和从右边扫描最后一个（窗口内从左边数第一个）的最大值，也就是下面这样

```java
res[j] = Math.max(maxRight[i], maxLeft[i + k - 1]);
```

为什么要这样选，大家可以想一下，因为如果选择从左边扫描的第一个值的话，那么这个值可能不是当前窗口内的值，同理从右边扫描的也一样。

<br/>

搞懂了上面的分析过程代码就很容易写了

```java
public int[] maxSlidingWindow(int[] nums, int k) {
    int len = nums.length;
    int[] maxLeft = new int[len];
    int[] maxRight = new int[len];
    //从左往右窗口的第一个最大值默认是数组第一个值
    maxLeft[0] = nums[0];
    //从右往左窗口的最后一个最大值是数组的最后一个值
    maxRight[len - 1] = nums[len - 1];

    for (int i = 1; i < len; i++) {
        //这里分别计算从前往后窗口的最大值和从后往前窗口的最大值。要搞懂这里的判断，如果
        //i % k == 0，表示到了下一个窗口
        maxLeft[i] = (i % k == 0) ? nums[i] : Math.max(maxLeft[i - 1], nums[i]);
        int j = len - i - 1;
        maxRight[j] = ((j + 1) % k == 0) ? nums[j] : Math.max(maxRight[j + 1], nums[j]);
    }
    //返回的结果值
    int[] res = new int[len - k + 1];
    for (int i = 0, j = 0; i < res.length; i++) {
        //取每个窗口内从左往右扫描的最后一个值和从右往左扫描的最后
        //一个值(如果从左边数是第一个)的最大值
        res[j++] = Math.max(maxRight[i], maxLeft[i + k - 1]);
    }
    return res;
}
```

<br>

#### 4，总结

滑动窗口题，第一种暴力求解一般都能想到，但效率很差，最常见的就是第2种使用双端队列，第3种方式效率也挺高的，但一般不太容易想到。

<br>

参照

[剑指 Offer 59. 滑动窗口的最大值-I](https://github.com/sdwwld/leetCode/blob/master/src/main/java/com/wld/java/offer/剑指Offer59-I.md)





![](https://img-blog.csdnimg.cn/20200807155236311.png)

