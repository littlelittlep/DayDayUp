####  week 3

#### 04.12  

补一补被我搁置的 kpi 们

##### 837. 新21点

题目要求

1、在**[1, maxPts]** 中抽取数字累加，大于**k**时停止

2、求问所有可能的累加结果中，不超过**n**的概率

每一个新数字都可能有很多种新结果啊。。

最好考虑的情况是大于k 的数字？

看一眼正规的题解思路吧：

> 假设 dp[x] 为她手上牌面为x时，能获胜的概率，那么这个概率应该是：
> dp[x]=1/w * (dp[x+1]+dp[x+2]+dp[x+3]...+dp[x+w])
> 因为抽取的牌面机会都是均等的，她能抽取的面值在 [1,W] 之间，所以将概率之和平均一下就是 dp[x] 的概率。

先完成：dp[x] 为手上牌面为x时获胜的概率

这个概率如何计算呢：

1、当前 x>k 概率已知 0 或 1

2、当前 x<k 则取 dp[x+ random] 的概率：极端情况：k<x+max<n 则概率为0

**好像这题得倒着遍历**

**dp[x] 为 x+1~x+max概率和除以max**

```java
/*
 * @lc app=leetcode.cn id=837 lang=java
 *
 * [837] 新 21 点
 */

// @lc code=start
class Solution {
    public double new21Game(int n, int k, int maxPts) {
        double[] dp=new double[k+maxPts];
        if(k-1+maxPts<=n){return 1.0;}
        double plus=0;
        for(int i=k;i<k+maxPts-1;i++){
            if(i<=n){dp[i]=1.0;}
            else{dp[i]=0;}
            plus+=dp[i];
        }
        for(int i=k-1;i>=0;i--){
            dp[i]=plus/maxPts;
            plus=plus+dp[i]-dp[i+maxPts];
        } 
        return dp[0];

    }
}
// @lc code=end


```

属于是在答案的帮助下完成了



#####  70. 爬楼梯

不愧是简单题，经过多日动态规划dp数组的折磨

五分钟内写完了

```java
/*
 * @lc app=leetcode.cn id=70 lang=java
 *
 * [70] 爬楼梯
 */

// @lc code=start
class Solution {
    public int climbStairs(int n) {
        int[] dp=new int[n+1];
        for(int i=0;i<=n;i++){
            if(i<=2){dp[i]=i;}
            else{
                dp[i]=dp[i-1]+dp[i-2];
            }
        }
        return dp[n];
    }
}
// @lc code=end


```
#####  0413  

先浅学一下01背包

创建一个状态矩阵f，横坐标 i 是物体编号，纵坐标 j 为背包容量。

i 代表对i件物体做决策，有两种方式—放入背包和不放入背包。
j 表示当前背包剩余的容量。

将该问题转换成01背包解决的关键：挑出一个背包的元素使得其和等于数组的元素和的一半

只要放入背包容量不超就放？不对吧，放入背包的判断条件是什么

```
dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i]];
```

这是什么神奇的。。。
