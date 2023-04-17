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
####  背包问题

> 分类解题模板
>
> 背包问题大体的解题模板是两层循环，分别遍历物品nums和背包容量target，然后写转移方程，
> 根据背包的分类我们确定物品和容量遍历的先后顺序，根据问题的分类我们确定状态转移方程的写法
>
> 首先是背包分类的模板：
> 1、0/1背包：外循环nums,内循环target,target倒序且target>=nums[i];
> 2、完全背包：外循环nums,内循环target,target正序且target>=nums[i];
> 3、组合背包：外循环target,内循环nums,target正序且target>=nums[i];
> 4、分组背包：这个比较特殊，需要三重循环：外循环背包bags,内部两层循环根据题目的要求转化为1,2,3三种背包类型的模板
>
> 然后是问题分类的模板：
> 1、最值问题: dp[i] = max/min(dp[i], dp[i-nums]+1)或dp[i] = max/min(dp[i], dp[i-num]+nums);
> 2、存在问题(bool)：dp[i]=dp[i]||dp[i-num];
> 3、组合问题：dp[i]+=dp[i-num];
>
> 这样遇到问题将两个模板往上一套大部分问题就可以迎刃而解
> 下面看一下具体的题目分析：
>
> 作者：eh-xing-qing
> 链接：https://leetcode.cn/problems/last-stone-weight-ii/solution/yi-pian-wen-zhang-chi-tou-bei-bao-wen-ti-5lfv/
> 来源：力扣（LeetCode）

##### 04.13

先浅学一下01背包

创建一个状态矩阵f，横坐标 i 是物体编号，纵坐标 j 为背包容量。

i 代表对i件物体做决策，有两种方式—放入背包和不放入背包。 j 表示当前背包剩余的容量。

将该问题转换成01背包解决的关键：挑出一个背包的元素使得其和等于数组的元素和的一半

又因为属于 存在类问题：

```
dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i]];
```

本来没太看懂，但参考题解才知道dp是bool类型二维数组

那它的逻辑就是把目前的物体 i 在背包容量为 j 时放入背包是否能得到true

按着方法套着写一下：

```
class Solution {
    public boolean canPartition(int[] nums) {

        int sum=0;
        int maxNum=0;
        for(int i=0;i<nums.length;i++){
            sum+=nums[i];
            maxNum = Math.max(maxNum, nums[i]);
        }
        if(sum%2!=0){return false;}
        int target=sum/2;
        if(maxNum>target){return false;}
        
        boolean[][] dp = new boolean[nums.length][target + 1];

        // 先填表格第 0 行，第 1 个数只能让容积为它自己的背包恰好装满
        if (nums[0] <= target) {
            dp[0][nums[0]] = true;
        }
        for(int i=1;i<nums.length;i++){
            int num=nums[i];
            for(int j=0;j<=target;j++){
                dp[i][j]=dp[i-1][j];
                if(j==nums[i]){
                    dp[i][j]=true;
                    continue;
                }
                else if(nums[i]<j){
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i]];
                }
            }
        }
        return dp[nums.length-1][target];
    }
}
```



#####  04.16

#####  1049. 最后一块石头的重量

属于01背包最值问题？

> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

首先复习背包的思路：

dp i j , j 记录背包此时能装下的最大重量，i 指第 i 个物品

题目是尽量不剩下石头？

**问题转化为：把一堆石头分成两堆,求两堆石头重量差最小值**

居然回到昨天的题了。。。
