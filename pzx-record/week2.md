####  2023.04.03

题3：最长无重复字符子串

第一版：

设置两个指针和一个判断重复的“字母集”

不发生重复的时候，固定左边界 i，移动右边界 j，cnt++

发生重复时，先判断记录max，再将 i右移并移除该元素，cnt--

但当 i右移到等于j时，要右移j

```java
/*
 * @lc app=leetcode.cn id=3 lang=java
 *
 * [3] 无重复字符的最长子串
 */

// @lc code=start
class Solution {
    public int lengthOfLongestSubstring(String s) { 
        int i=0;int j=1;
        int[] dp=new int[s.length()];
        int[] set=new int[26];
        int cnt=1;
        int max=0;
        set[s.charAt(0)-97]=1;
        while(i<j && j<s.length()-1){
                int n=s.charAt(j)-97;
                if(set[n]==0){
                    set[n]=1;
                    cnt++;
                    j++;
                }
                else{
                    max=Math.max(max,cnt);
                    int m=s.charAt(i)-97;
                    set[m]=0;
                    i++;
                    cnt--;
                    if(i==j){j++;cnt=0;}
                }
        }

        return max;
    }
}
// @lc code=end

```

第一次提交：“pwwwkew"通过 ;  "pwwkew"不通过，奇怪

太复杂了，先放着



题49：字母异位词分组

初始想法：

1、对于每个单词：记录每个字母出现的次数

2、同时用字典记录，如果该数字字符串已出现过，则加入对应的列表
3、找到对应列表的方式是：将列表的第一位设置为该记录的数字字符串进行匹配
还没有好好看别人的题解，不知道这个题的考点
以极低的性能通过了测试：

```python
#
# @lc app=leetcode.cn id=49 lang=python3
#
# [49] 字母异位词分组
#

# @lc code=start
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        d={"字典":"999"}
        length=len(strs)
        if length==1:
            l=[]
            l.append(strs)
            return l
        l=[[] for i in range(length)]
        team=0
        for i in strs:
            zero='0'*26
            for m in i:
                n=ord(m)-97
                tmp=list(zero)
                tmp[n]=chr(ord(zero[n])+1)
                zero=''.join(tmp)
            if d.get(zero)!=None:
                for k in range(len(l)):
                    if l[k]!=[]:
                        if l[k][0]==zero:
                            l[k].append(i)
            else:
                l[team].append(zero)
                l[team].append(i)
                d[zero]=i
                team+=1
        newlist=[x for x in l if x]
        for m in newlist:
            del(m[0])
        
        return newlist
       

# @lc code=end


```
时间：5.45%
不知道字典查询的复杂度，所以不太好计算

空间：24.3%
