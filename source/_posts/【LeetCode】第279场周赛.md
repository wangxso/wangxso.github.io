title: 【LeetCode】第279场周赛
date: 2022-03-03 10:07:59
categories: 算法
tags: []
---
# 第279场周赛

## 第一题 2164. 对奇偶下标分别排序

### 题意

> 给你一个下标从 0 开始的整数数组 nums 。根据下述规则重排 nums 中的值：
> 按 非递增 顺序排列 nums 奇数下标 上的所有值。
> 举个例子，如果排序前 nums = [4,1,2,3] ，对奇数下标的值排序后变为 [4,3,2,1] 。奇数下标 1 和 3 的值按照非递增顺序重排。
> 按 非递减 顺序排列 nums 偶数下标 上的所有值。
> 举个例子，如果排序前 nums = [4,1,2,3] ，对偶数下标的值排序后变为 [2,1,4,3] 。偶数下标 0 和 2 的值按照非递减顺序重排。
> 返回重排 nums 的值之后形成的数组。
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/sort-even-and-odd-indices-independently
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

一个水题，但是我脑子嗡嗡的，写了半个小时。

我的思路是把奇数位的放入一个数组排序，然后把偶数位的放入一个数组排序。然后合并。

算法复杂度应该是O(N), 精确点就是2N。

### 代码

```java
import java.util.*;
class Solution {
   public int[] sortEvenOdd(int[] nums) {
        if(nums.length <= 2) return nums;
        List<Integer> odd = new ArrayList<>();
        List<Integer> even = new ArrayList<>();
        for(int i = 0; i < nums.length; i++) {
            if(i % 2 != 0) {
                odd.add(nums[i]);
            } else {
                even.add(nums[i]);
            }
        }
        odd.sort((o1, o2) -> o2 - o1);
        Collections.sort(even);
        int oddIdx = 0;
        int evenIdx = 0;
        for (int i = 0; i < nums.length; i++) {
            if (i % 2 != 0) {
                Integer val = odd.get(oddIdx++);
                nums[i] = val;
            } else {
                Integer val = even.get(evenIdx++);
                nums[i] = val;
            }
        }
        return nums;
    }
}

```

## 第二题 	[2165. 重排数字的最小值](https://leetcode-cn.com/problems/smallest-value-of-the-rearranged-number/)

### 题目

> 给你一个整数 num 。重排 num 中的各位数字，使其值 最小化 且不含 任何 前导零。
>
> 返回不含前导零且值最小的重排数字。
>
> 注意，重排各位数字后，num 的符号不会改变。
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/smallest-value-of-the-rearranged-number
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

- 对于正整数我们只需要把每一位数字取出，如[3,1,0]，然后对这个数组进行排序[0, 1, 3]，如果发现前导为0，我们就寻找第一个不为0的数字和第一位交换。这里也就是```0 <-> 1```，最后变为[1, 0, 3]。
  - [4, 0, 0, 3, 2] ----排序---> [0, 0, 2, 3, 4] ---交换->[2, 0, 0, 3, 4]
- 对于负数，我们取它的绝对值，然后要求调整位置使得这个绝对值最大，我们就可以把数字从大到小排序，这样就不会出现前导为0，我们直接算出就可以。注意最后要写成负数。
  - -[7, 6, 0, 5] ---->-[7,6,5,0]

### 代码

```java
class Solution {
    public long smallestNumber(long num) {
        if(num == 0) return 0;
        List<Long> list = new ArrayList<>();
        long ans = 0;
        if (num < 0) {
            num = -num;
            long tmp = num;
            while (tmp > 0) {
                long remind = tmp % 10;
                tmp /= 10;
                list.add(remind);
            }
            list.sort((o1, o2) -> (int) (o2 - o1));
            long pow = 1;
            for (int i = list.size()-1; i >= 0; i--) {
                long n = list.get(i);
                ans += pow * n;
                pow *= 10;
            }
            ans = -ans;
        } else {
            long tmp = num;
            while (tmp > 0) {
                long remind = tmp % 10;
                tmp /= 10;
                list.add(remind);
            }
            Collections.sort(list);
            if (list.get(0) == 0) {
                int end = 0;
                for (int i = 0; i < list.size(); i++) {
                    if (list.get(i) != 0) {
                        end = i;
                        break;
                    }
                }
                long next = list.get(end);
                list.set(end, 0L);
                list.set(0, next);
            }
            long pow = 1;
            for (int i = list.size()-1; i >= 0; i--) {
                long n = list.get(i);
                ans += pow * n;
                pow *= 10;
            }
        }
        return ans;
    }
}
```

## 第三题 [2166. 设计位集](https://leetcode-cn.com/problems/design-bitset/)

### 题目

> 位集 Bitset 是一种能以紧凑形式存储位的数据结构。
>
> 请你实现 Bitset 类。
>
> Bitset(int size) 用 size 个位初始化 Bitset ，所有位都是 0 。
> void fix(int idx) 将下标为 idx 的位上的值更新为 1 。如果值已经是 1 ，则不会发生任何改变。
> void unfix(int idx) 将下标为 idx 的位上的值更新为 0 。如果值已经是 0 ，则不会发生任何改变。
> void flip() 翻转 Bitset 中每一位上的值。换句话说，所有值为 0 的位将会变成 1 ，反之亦然。
> boolean all() 检查 Bitset 中 每一位 的值是否都是 1 。如果满足此条件，返回 true ；否则，返回 false 。
> boolean one() 检查 Bitset 中 是否 至少一位 的值是 1 。如果满足此条件，返回 true ；否则，返回 false 。
> int count() 返回 Bitset 中值为 1 的位的 总数 。
> String toString() 返回 Bitset 的当前组成情况。注意，在结果字符串中，第 i 个下标处的字符应该与 Bitset 中的第 i 位一致。
>
> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/design-bitset
> 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 思路

一开始沙比Σ(☉▽☉"a，没看数据的范围

- `1 <= size <= 105`
- `0 <= idx <= size - 1`

然后直接用char(16bit)做直接爆了，然后才看了下官方的做法。

#### 错误的方法，用char进行位运算直接做

错误的做法如下。




```java
class Bitset {
    int size;
    char bitSet;
    public Bitset(int size) {
        this.size = size;
        this.bitSet = 0;
    }

    public void fix(int idx) {
        int change = (int) Math.pow(2, this.size - idx - 1);
        this.bitSet = (char) (this.bitSet | change);
    }

    public void unfix(int idx) {
        int change = ~(int) Math.pow(2, this.size - idx - 1);
        this.bitSet = (char) (this.bitSet & change);
    }

    public void flip() {
        this.bitSet = (char) ((~this.bitSet));
    }

    public boolean all() {
        int tmp = this.bitSet;
        int cnt = 0;
        while (cnt < this.size) {
            if ((tmp & 1) != 1) {
                return false;
            }
            cnt++;
            tmp >>= 1;
        }
        return true;
    }

    public boolean one() {
        int tmp = this.bitSet;
        int cnt = 0;
        while (cnt < this.size) {
            if ((tmp & 1) == 1) {
                return true;
            }
            cnt++;
            tmp >>= 1;
        }
        return false;
    }

    public int count() {
        int cnt = 0;
        int tmp = this.bitSet;
        int p = 0;
        while (p < this.size) {
            if ((tmp & 1) == 1) {
                cnt++;
            }
            p++;
            tmp >>= 1;
        }
        return cnt;
    }

    public String toString() {
        int tmp = this.bitSet;
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < this.size; i++) {
            int remind =tmp % 2;
            tmp /= 2;
            sb.append(remind);
        }
        sb.reverse();
        return sb.toString();
    }
}
```

#### 正确的方法，用数组模拟bit位进行运算

我们需要知道如果经过偶数次的```flip```，二进制数不变，所以我们用一个```reversed```的二进制位来表示是奇数次还是偶数次, ```flip()```函数不真正改变```arr[]```里面的数字，而是在需要运算的时候才使用。

```java
public void fix(int idx) {
        // 如果它arr[idx]通过反转后为0的话
        if ((arr[idx] ^ reversed) == 0) {
            // 那么就把这个位数变为1. 异或的规则是同0异1，也就是 0^1=1, 1^1=0, 0^0=0, 1^0=1
            arr[idx] ^= 1;
            ++cnt;
        }
}
```

```java
public void unfix(int idx) {
        // 如果它arr[idx]通过反转后为1的话
        if ((arr[idx] ^ reversed) == 1) {
            // 把这个变成0
            arr[idx] ^= 1;
            --cnt;
        }
 }
```

```java
public void flip() {
        // 记录反转次数， 0 异或 1 = 1 -> 1 异或 1 = 0
    	// 仅记录反转次数，不参与计算
        reversed ^= 1;
        cnt = arr.length - cnt;
}
```

```java
public boolean all() {
        return cnt == arr.length;
}

public boolean one() {
    return cnt > 0;
}

public int count() {
    return cnt;
}
```

```java
public String toString() {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < arr.length; i++) {
            // 最后输出的时候把反转加上
            sb.append(arr[i] ^ reversed);
        }
        sb.reverse();
        return sb.toString();
}
```



### 代码

```java
class Bitset {
    int[] arr;
    int cnt ;
    int reversed;
    public Bitset(int size) {
        arr = new int[size];
        this.cnt = 0;
        this.reversed = 0;
    }

    public void fix(int idx) {
        // 如果它arr[idx]通过反转后为0的话
        if ((arr[idx] ^ reversed) == 0) {
            arr[idx] ^= 1;
            ++cnt;
        }
    }

    public void unfix(int idx) {
        // 如果它arr[idx]通过反转后为1的话
        if ((arr[idx] ^ reversed) == 1) {
            arr[idx] ^= 1;
            --cnt;
        }
    }

    public void flip() {
        // 记录反转次数， 0 异或 1 = 1 -> 1 异或 1 = 0
        reversed ^= 1;
        cnt = arr.length - cnt;
    }

        public boolean all() {
            return cnt == arr.length;
        }

        public boolean one() {
            return cnt > 0;
        }

        public int count() {
            return cnt;
        }

    public String toString() {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < arr.length; i++) {
            sb.append(arr[i] ^ reversed);
        }
        sb.reverse();
        return sb.toString();
    }
}
```

