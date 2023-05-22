# 第4周

## day1

### 负二进制相加

> **思路一**
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

> **思路二**
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
> 别人的题解（写的比我简洁duo'le）
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