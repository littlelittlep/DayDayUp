# 题解

# 2023.3.28 力扣28、

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

