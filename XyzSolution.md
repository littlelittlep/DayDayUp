# 题解

# 2023.3.28 力扣28（字符串匹配KMP）、力扣486、877（博弈动态规划）

## [`28、找出字符串中第一个匹配项的下标`](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

```C++
class Solution {
public:
    int strStr(string haystack, string needle) {
        //haystack的指针
        int left=0;
        //needle的指针
        int right=0;
        int len=needle.size();
        for(;left<haystack.size();){
            //如果相等。两个指针都往前走
            if(haystack[left]==needle[right]){
                left++;
                right++;
            }
            if(haystack[left]!=needle[right]){
                //right达到长度，说明已经有匹配完成的，直接返回
                if(right==len){
                    return left-len;
                }
                //right达不到长度，说明未匹配完成，将left回退，然后right清零
                else{
                    left=left-right+1;
                    right=0;
                }
            }
        }
        //有可能最后刚好匹配完，因跳出循环，所以上面没有return这里需要判断一下
        if(right==len) return left-len;
        else return -1;

    }
};
//JS代码将 int 换成 let 而已，故不再写 js 版本代码
```

## [`486、预测赢家`](https://leetcode.cn/problems/predict-the-winner/)

```C++
class Solution {
public:
    bool PredictTheWinner(vector<int>& nums) {
        //dp的含义，该ij区间先手和后手能拿到的值
        //初始化dp右上角
        pair<int,int> dp[20][20];
        for(int i=0;i<20;i++)
        for(int j=i;j<20;j++){
            dp[i][j].first=0;
            dp[i][j].second=0;
        }
        //初始化对角线
        //情况：如果只有一个数，先手可以选择该数，后手无数可选为0
        for(int i=0;i<nums.size();i++){
            dp[i][i].first=nums[i];
            dp[i][i].second=0;
        }
        for(int i=nums.size()-2;i>=0;i--)
        for(int j=i+1;j<nums.size();j++){
            //算出选左选右右的值
            //如果选左，那么下一次是后手
            int left=nums[i]+dp[i+1][j].second;
            int right=nums[j]+dp[i][j-1].second;
            //选了左，当前的先手和后手进行更新
            if(left>right){
                dp[i][j].first=left;
                dp[i][j].second=dp[i+1][j].first;
            }
            else{
                dp[i][j].first = right;
                dp[i][j].second = dp[i][j-1].first;
            }
        }
        //最终结果就是区间0~size()-1的先手和后手进行比较
        pair<int,int> p=dp[0][nums.size()-1];
        if((p.first-p.second)>=0) return true;
        return false;
    }
};
```

# 2023.3.29 力扣322（零钱兑换）、力扣

## [`322、零钱兑换`](https://leetcode.cn/problems/coin-change/)

```C++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        //dp[i]代表金额为i的最少硬币个数
        int dp[10001]={[0 ... 10000] = 10002};
        //初始化
        for(int i=0;i<coins.size();i++){
            //硬币面值超过金额，不考虑（此处=注意）
            if(coins[i]<=amount)
            dp[coins[i]]=1;
        }
        dp[0]=0;
        //动态规划转移方程
        for(int i=1;i<=amount;i++){
            if(dp[i]==10002){
                for(int j=0;j<coins.size();j++){
                    if(i-coins[j]>0&&dp[(i-coins[j])]!=10002)
                    dp[i]=min(1+dp[(i-coins[j])],dp[i]);
                }
            } 
        }
        if(dp[amount]==10002) return -1;
        else return dp[amount];
    }
};
```

## [`300、最长递增子序列`](https://leetcode.cn/problems/longest-increasing-subsequence/submissions/)

```C++
//此题的难点在于如何定义dp
//没想出如何定义dp，看了一下dp定义，然后自己写的，一次就过！
//一个好的dp定义真的很重要！！！！
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        //dp[i]表示以nums[i]结尾的最长严格递增子序列长度
        int dp[2500];
        //初始化数组
        for(int i=0;i<2500;i++){
            dp[i]=1;
        }
        //dp[i]的值，为小于i的j,即dp[j]+1(且nums[i]>nums[j])
        for(int i=1;i<nums.size();i++){
            for(int j=0;j<i;j++){
                if(nums[i]>nums[j]){
                    dp[i]=max(dp[j]+1,dp[i]);
                }
            }
        }
        //输出dp数组中的最大值
        int temp=0;
        for(int i=0;i<nums.size();i++){
            if(dp[i]>temp) temp=dp[i];
        }
        return temp;
    }
};
```

**`二分法查找解法`**

> 最长递增子序列和一种叫做 *patience game*的纸牌游戏有关，有一种排序算法叫做***patience sorting***
>
> **纸牌遍历顺序拿取，规则如下:**只能把点数小的牌压到点数比它大的牌上；如果当前牌点数较大没有可以放置的堆，则新建一个堆，把这张牌放进去；如果当前牌有多个堆可供选择，则选择最左边的那一堆放置。最终得到的堆数就是最长递增子序列长度
>
> **思路如下**
>
> > - 用vector创建一个数组放每一个堆的堆顶，对于每一张牌，用二分查找找到它应插入的位置，如果找不到，新建一个堆
> > - 遍历完所有牌以后，数组的长度就是最长递增子序列的长度了

# 2023.3.31 力扣354（俄罗斯套娃信封）

## [`354、俄罗斯套娃信封`](https://leetcode.cn/problems/russian-doll-envelopes/)

> **思路**
>
> 1. 先对二维中的第一维进行非降序排序
>
>    自定义 *cmp* 函数 (这里复习上次最长递增子序列中提到，`对于第二位要采取非升序排序`)
>
> 2. 对于第二位进行找到最长递增子序列即可
>
>    一开始用昨天29的求最长递增子序列的方法，时间复杂度太高。。。
>
>    所以复习了那篇博客中的二分查找解法（详见29笔记补充），终于AC了
>
> ```C++
> class Solution {
> private:
>     //自定义比较函数，第一维非降序排列，当第一维数字相同，第二位按照非升序排列
>  static bool  cmp(const vector<int> &a, const vector<int> &b){
>      if(a[0]<b[0]) return true;
>      else if(a[0]==b[0]&&a[1]<=b[1]) return false;
>      else if(a[0]==b[0]&&a[1]>b[1]) return true;
>      else return false;
> 
>  }
> public:
>     int maxEnvelopes(vector<vector<int>>& envelopes) {
>         sort(envelopes.begin(),envelopes.end(),cmp);
>         int dp[100001]={0};
>         //数组初始化
>         for(int i=0;i<envelopes.size();i++){
>             dp[i]=1;
>         }
>         //堆数
>         int piles=0;
>         vector<int> res;
>         for(int i=0;i<envelopes.size();i++){
>             int left=0,right=res.size();
>             //二分查找确定是能找到堆插入还是新建堆
>             while(left<right){
>                 int mid=(left+right)/2;
>                 if(res[mid]>envelopes[i][1]){
>                     right=mid;
>                 }
>                 if(res[mid]<envelopes[i][1]){
>                     left=mid+1;
>                 }
>                 else{
>                     right=mid;
>                 }
>             }
>             if(left==piles) {
>                 res.push_back(envelopes[i][1]);
>                 piles++;
>             }
>             res[left]=envelopes[i][1];
>         }
>         //向量的长度就是最长递增子序列
>         return res.size();
>     }
> };
> ```
>
> 

