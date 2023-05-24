# 第4周

## day1

### 负二进制相加

> ### 思路一
>
> 直接算出两个数组的和，相加，再转换成二进制
>
> ==出现的问题==
>
> 1. 求幂用pow必用^好
> 2. 边界为0没有考虑
> 3. 数值的和超出long long范围 👉 放弃这种写法

```cpp
class Solution {
public:
    vector<int> addNegabinary(vector<int>& arr1, vector<int>& arr2) {
        int sz1=arr1.size();
        int sz2=arr2.size();
        long long r1=0,r2=0;
        for(int i=0;i<sz1;i++){
            if(arr1[i]==1) r1+=pow(-2,sz1-1-i);
        }
        for(int i=0;i<sz2;i++){
            if(arr2[i]==1) r2+=pow(-2,sz2-1-i);
        }
        long long res=r1+r2;
        vector<int> re;
        if(res==0) {
            re.push_back(0);
            return re;
        }
        while(res!=0){
            if(res%(-2)==-1){
                res=res/(-2)+1;
                re.insert(re.begin(),1);
            }else{
                re.insert(re.begin(),res%(-2));
                res=res/(-2);
            }
        }
        return re;
    }
};
```

> ### 思路二
>
> 1. 对于负二进制而言，进位是-1，但是要注意如果`相加结果为-1`的特殊情况，如下表
>
> 2. 将两个数组逐位按照表格进行相加
>
> 3. 将长的数组逐位计算
>
> 4. 删除数组中的前置0
>
> 5. 如果数组空，则push一个0进去
>
>    | 第一个数  arr1 [ i ] | 第二个数 arr2 [ j ] | 进位 add 👉 相加结果                      |
>    | -------------------- | ------------------- | ---------------------------------------- |
>    | 0                    | 0                   | 0 / -1 / 1  👉 留0进0 / 留1进1 / 留1进0   |
>    | 0                    | 1                   | 0 / -1 / 1  👉 留1进0 / 留0进0 / 留0进-1  |
>    | 1                    | 0                   | 0 / -1 / 1  👉 留1进0 / 留0进0 / 留0进-1  |
>    | 1                    | 1                   | 0 / -1 / 1  👉 留0进-1 / 留1进0 / 留1进-1 |
>
> 代码如下
>
> ```cpp
> class Solution {
> public:
>     vector<int> addNegabinary(vector<int>& arr1, vector<int>& arr2) {
>         int i=arr1.size()-1,j=arr2.size()-1;
>         int add=0;
>         vector<int> res;
>         while(i>=0&&j>=0){
>             if(arr1[i]+arr2[j]+add==0){
>                 res.insert(res.begin(),0);
>                 add=0;
>             }
>             else if(arr1[i]+arr2[j]+add==-1){
>                 res.insert(res.begin(),1);
>                 add=1;
>             }
>             else if(arr1[i]+arr2[j]+add==1){
>                 res.insert(res.begin(),1);
>                 add=0;
>             }
>             else if(arr1[i]+arr2[j]+add==2){
>                 res.insert(res.begin(),0);
>                 add=-1;
>             }
>             else if(arr1[i]+arr2[j]+add==3){
>                 res.insert(res.begin(),1);
>                 add=-1;
>             }
>             i--;
>             j--;
>         }
>         while(i>=0){
>             if(arr1[i]+add==0){
>                 res.insert(res.begin(),0);
>                 add=0;
>             }
>             else if(arr1[i]+add==-1){
>                 res.insert(res.begin(),1);
>                 add=1;
>             }
>             else if(arr1[i]+add==1){
>                 res.insert(res.begin(),1);
>                 add=0;
>             }
>             else if(arr1[i]+add==2){
>                 res.insert(res.begin(),0);
>                 add=-1;
>             }
>             else if(arr1[i]+add==3){
>                 res.insert(res.begin(),1);
>                 add=-1;
>             }
>             i--;
>         }
>         while(j>=0){
>             if(arr2[j]+add==0){
>                 res.insert(res.begin(),0);
>                 add=0;
>             }
>             else if(arr2[j]+add==-1){
>                 res.insert(res.begin(),1);
>                 add=1;
>             }
>             else if(arr2[j]+add==1){
>                 res.insert(res.begin(),1);
>                 add=0;
>             }
>             else if(arr2[j]+add==2){
>                 res.insert(res.begin(),0);
>                 add=-1;
>             }
>             else if(arr2[j]+add==3){
>                 res.insert(res.begin(),1);
>                 add=-1;
>             }
>             j--;
>         }
>         if(add==-1){
>             res.insert(res.begin(),1);
>             res.insert(res.begin(),1);
>             return res;
>         }
>         while(res.size()>0&&res[0]==0){
>             res.erase(res.begin());
>         }
>         if(res.size()==0) res.push_back(0);
>         return res;
>     }
> };
> ```
>
> 
>
> ### 别人的题解（写的比我简洁duo'le）
>
> ```cpp
> class Solution {
> public:
>     vector<int> addNegabinary(vector<int>& arr1, vector<int>& arr2) {
>         int i = arr1.size() - 1, j = arr2.size() - 1;
>         vector<int> ans;
>         for (int c = 0; i >= 0 || j >= 0 || c; --i, --j) {
>             int a = i < 0 ? 0 : arr1[i];
>             int b = j < 0 ? 0 : arr2[j];
>             int x = a + b + c;
>             c = 0;
>             if (x >= 2) {
>                 x -= 2;
>                 c -= 1;
>             } else if (x == -1) {
>                 x = 1;
>                 c += 1;
>             }
>             ans.push_back(x);
>         }
>         while (ans.size() > 1 && ans.back() == 0) {
>             ans.pop_back();
>         }
>         reverse(ans.begin(), ans.end());
>         return ans;
>     }
> };
> 
> 作者：lcbin
> 链接：https://leetcode.cn/problems/adding-two-negabinary-numbers/solution/python3javacgotypescript-yi-ti-yi-jie-mo-mg0a/
> 来源：力扣（LeetCode）
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
> ```
>
> 

## day 2

### 受标签影响的最大值

> ### 思路一
>
> 1. 用一个map记录对应的标签的次数<标签，次数>
>
> 2. （有一个测试用例超出时间了，因此将原先的values数组按照值进行排序，方便后面剪枝，结果没用。。。。）
>
> 3. 然后就是回溯
>
>    这个回溯写了巨久，首先结束条件是达到数组的size，然后numWanted和useLimit进行判断（这部分放在for循环中）
>
> **<font color="red">结果：69/81</font>**
>
> ```cpp
> class Solution {
> private:
>     int sum=0;
>     int res=0;
>     map<int,int> mp;
>     void backtrack(vector<pair<int,int>>& values,int i, int numWanted, int useLimit,map<int,int> mp,int r){
>         //cout<<"进入:"<<i<<"  和:"<<r<<endl;
>         if(i>=values.size()) {
>             //cout<<"结束位置："<<i<<"  和:"<<r<<endl;
>             if(r>res){
>                 res=r;
>             }
>             return ;
>         }
>         for(int j=i;j<values.size();j++){
>             sum++;
>             mp[values[j].second]++;
>             if(sum>numWanted){
>                 //cout<<"结束位置："<<i<<"  和:"<<r<<endl;
>                 sum--;
>                 mp[values[j].second]--;
>                 if(r>res){
>                     res=r;
>                 }
>                 break;
>             }
>             if(mp[values[j].second]>useLimit){
>                 //cout<<"结束位置："<<i<<"  和:"<<r<<endl;
>                 sum--;
>                 mp[values[j].second]--;
>                 if(r>res){
>                     res=r;
>                 }
>                 continue;
>             }
>             r+=values[j].first;
>             backtrack(values,j+1,numWanted,useLimit,mp,r);
>             r-=values[j].first;
>             sum--;
>             mp[values[j].second]--;
>         }
>     }
> public:
>     int largestValsFromLabels(vector<int>& values, vector<int>& labels, int numWanted, int useLimit) {
>         //建立map
>         for(int i=0;i<labels.size();i++){
>             mp[labels[i]]=0;
>         }
>         //对vector按照值的大小进行排序，便于后续剪枝
>         vector<pair<int,int>> vec;
>         for(int i=0;i<values.size();i++){
>             vec.push_back(pair<int,int>(values[i],labels[i]));
>         }
>         sort(vec.begin(),vec.end(),[](const pair<int, int>& a, const pair<int, int>& b){ return a.first > b.first; });
>         //回溯找子集，大于numWanted或者大于useLimit剪枝
>         backtrack(vec,0,numWanted,useLimit,mp,0);
>         return res;
>     }
> };
> ```
>

> ### 思路二
>
> 1. 对于两个数组进行排序比较难，可以开新数组对values排序后的顺序记录下来
> 2. 然后map用来记录useLimit
> 3. 使用贪心
>
> ```cpp
> class Solution {
> public:
>     int largestValsFromLabels(vector<int>& values, vector<int>& labels, int numWanted, int useLimit) {
>         int n = values.size();
>         //开辟新数组，用来记录排序后原先的顺序
>         vector<int> id(n);
>         iota(id.begin(), id.end(), 0);
>         sort(id.begin(), id.end(), [&](int i, int j) {
>             return values[i] > values[j];
>         });
> 
>         int ans = 0, choose = 0;
>         unordered_map<int, int> cnt;
>         //从大到小排序，贪心，首先是数字要最大，然后是不超过数字数量限制和标签shu'li'a
>         for (int i = 0; i < n && choose < numWanted; ++i) {
>             int label = labels[id[i]];
>             if (cnt[label] == useLimit) {
>                 continue;
>             }
>             ++choose;
>             ans += values[id[i]];
>             ++cnt[label];
>         }
>         return ans;
>     }
> };
> 
> 作者：LeetCode-Solution
> 链接：https://leetcode.cn/problems/largest-values-from-labels/solution/shou-biao-qian-ying-xiang-de-zui-da-zhi-5h9ll/
> 来源：力扣（LeetCode）
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
> ```
>

## day 3

### 盛水最多的容器

> **思路**
>
> **双指针**，头尾，每次移短板，移动短的更可能赢
>
> ```cpp
> class Solution {
> public:
>     int maxArea(vector<int>& height) {
>     int v=0;
>     int i=0;
>     int j=height.size()-1;
>    
>     while(i<=j)
>     {
>         v=min(height[i],height[j])*(j-i)>v?min(height[i],height[j])*(j-i):v;
>         height[i]>height[j]?j--:i++;
>     }
>     return v;
>     }
> };
> ```