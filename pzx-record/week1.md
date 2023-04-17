####  2023.03.28

预测赢家类动态规划：

我的理解思路：

1、dp[i] [j]数组里存放的是在该区间内可赢得的最大值

2、选择右边，减去后手的dp[i] [j-1]；选择左边则减去后手的dp[i+1] [j]

3、取Max(左，右)=dp[i] [j]

以上均好理解，但接下来如何遍历呢，递归和迭代两种?？

```c++
 int right=nums[j]-getmax(i,j-1);
 int left=nums[i]-getmax(i+1,j);
 dp[i][j]=Max(right,left);
 return dp[i][j];
```


更正：dp数组开成[n+1] [n+1]; while 改成if

第一次提交：gch说用dp 就不用递归，不懂

添加了i==j时返回0的条件：

然而，突然发现用了递归就不是动态规划了啊。。。还是得遍历

不会遍历，只能抄一抄背下来了：

1、先给i==j 的情况赋值

2、行从后向前遍历，从length-2到0

3、列从（i+1,length)遍历，即上半部分


```java
class Solution {

    public boolean PredictTheWinner(int[] nums) {
        int n=nums.length-1;
        int[][] dp=new int [n+1][n+1];
         for (int i = 0; i < n; i++) {
            dp[i][i] = nums[i];
        }
        for(int i=n-1;i>=0;i--){
            for(int j=i+1;j<n+1;j++){
                int right=nums[j]-dp[i][j-1];
                int left=nums[i]-dp[i+1][j];
                dp[i][j]=Math.max(right,left);
            }
        }
        if(dp[0][n]>=0){return true;}
        return false;
    }
}
```

终于通过了，但遍历还是要记一下：

行从倒数第二行 length-2开始，列从 i 上面到 length;



题2：石子游戏：

考虑与题一的区别：

堆数变成偶数；



第一题的方法可以通过，但应该还能优化：

看了题解，没太会怎么优化

####  2023.03.29

题322：

有点像青蛙跳台阶的加强版？

dp数组里存到达某个值的最少硬币个数，emmm那应该是一维数组了

先设置已有面额：dp[i]=1（有一个用例有过大的面额，所以设置把比目标amount小的已有面额置一，毕竟比已有面额大的也用不上）

遍历：数值从0到目标amount，遍历面额，取最小路径min

根据各种各样的报错修修改改终于做完了：

```java
class Solution {
    public int coinChange(int[] coins, int amount) {
        if(amount==0){return 0;}
        int n=coins.length-1;
        if(n==0){
            if(amount%coins[0]==0){return amount/coins[0];}
            else{return -1;}
        }
        int[] dp=new int[amount+n]; 
        for(int i=0;i<=n;i++){
            int num=coins[i];
            if(coins[i]<=amount)
                dp[num]=1;
        }
        
        for(int i=0;i<=amount;i++){
            int min=0;
            for(int j=0;j<=n;j++){
                int dif=i-coins[j];
                if(dif==0){min=1;}
                if(dif>0){
                    if(dp[dif]!=0){
                        if(min==0)
                            min=dp[dif]+1;
                        else
                            min=Math.min(min,dp[dif]+1);}
                }
            }
            dp[i]=min;
        }
        if(dp[amount]==0){return -1;}
        else{return dp[amount];}
    }
}
```

对了要评估一下复杂度？

遍历了coin , 和amount * coin

空间复杂度应该就是dp数组的大小？



题300：最长递增子序列

记得dp数组应该是存放到第i个元素（以第i个元素结尾）的最长递增

只要知道计算 dp[i] 的时候要把之前的每个 dp[j] 遍历一下，同样记录max的值，如果满足递增则取 Math.max(dp[j]+1, max)

每一次对比记录Max的值，最后得到最长的max(dp[i])

```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        if(nums.length == 0) return 0;
        int[] dp = new int[nums.length];
        int res = 0;
        Arrays.fill(dp, 1);
        for(int i = 0; i < nums.length; i++) {
            for(int j = 0; j < i; j++) {
                if(nums[j] < nums[i]) dp[i] = Math.max(dp[i], dp[j] + 1);
            }
            res = Math.max(res, dp[i]);
        }
        return res;
    }
}

```

易知时间复杂度是O(n2)

空间复杂度是dp数组的大小O(n)


#### 2023.03.30

目前只有做题思路：
题354：俄罗斯套娃信封问题

是个hard，但既然只是二维版的最长递增子序列

那就按300的思路叭

emmmm但是发现一个问题，这个不是递增

那就一直记录最大和最小的信封？不是

看了题解原来如此，先按宽度排序，再按高度做最长递增子序列

为保证不能用宽度重合的信封，需将同样宽度的降序排列

看了你的题解，可能O(n2)时间太长了，得用二分查找


#### 2023.04.01

