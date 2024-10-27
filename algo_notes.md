# 一维差分

对一个区间X[a,b]进行加减n的操作，等价于对一个差分区间的X[a]+-n,X[b+1]+-n，然后用一个pre变量，依次累加，可以还原原本的X。

这样可以应对对一个区间的多次修改。

## 例子

给你一个下标从 **0** 开始的二维整数数组 `nums` 表示汽车停放在数轴上的坐标。对于任意下标 `i`，`nums[i] = [starti, endi]` ，其中 `starti` 是第 `i` 辆车的起点，`endi` 是第 `i` 辆车的终点。

返回数轴上被车 **任意部分** 覆盖的整数点的数目。

## code

```cpp
class Solution {
public:
    int numberOfPoints(vector<vector<int>>& nums) {
        vector<int> v(102);
        for(auto i:nums){
            v[i[0]]++;
            v[i[1]+1]--;//这里是否+1，要考虑原题中是开区间还是闭区间。
        }
        int ans=0;
        int t=0;
        for(auto i:v){
            t+=i;
            ans+=!!t;
        }
        return ans;
    }
};
```

# 模和快速幂

$$
(a+b)mod\  c=(a\ mod\ c+b\ mod \ c)mod\ c
\\ab\%c=(a\%c)(b\%c)\%c
$$

负数取模：
$$
x\ mod \ y =(x \ mod \ y + \ y)\ mod \ y
$$
除法取模：
$$
\frac{a}{b}\ mod \ p=(a\cdot b^{p-2} )\ mod \ p
$$
要求：a是b的倍数且b、p互质。

快速幂：

求$a^{b}$，可以用快速幂的方法，具体是便利每个位置，遇到b的二进制表示为1的时候就乘一下。

## code

```cpp
const int MOD = 1_000_000_007;
int a, int b;
// 加
(a + b) % MOD;

// 减
(a - b + MOD) % MOD;

// 取模到 [0,MOD-1] 中，无论正负
(a % MOD + MOD) % MOD;

// 乘
a * b % MOD;

// 多个数相乘，要步步取模，防止溢出
a * b % MOD * c % MOD;

// 除（MOD 是质数且 b 不是 MOD 的倍数）
a * qpow(b, MOD - 2, MOD) % MOD;

```

```cpp
class Solution {
public:
    double myPow(double x, int n) {
        long long t=n;
        if (t < 0) {
            t = -t;
            x = 1 / x;
        }
        double ans = 1;
        while (t) {
            if (t & 1) {
                ans *= x;
            }
            x = x * x;
            t = t >> 1;
        }
        return ans;
    }
};

//递归写法
class Solution {
public:
    double quickPow(double x,long long N){
        if(N==0)return 1;
        double y=quickPow(x,N/2);
        return N%2==0?y*y:y*y*x;
    }
    double myPow(double x, int n) {
        long long N=n;
        return N>=0?quickPow(x,N):1/quickPow(x,-N);
    }
};
```

## 例子

给你一个下标从 **0** 开始的二维数组 `variables` ，其中 `variables[i] = [ai, bi, ci, mi]`，以及一个整数 `target` 。

如果满足以下公式，则下标 `i` 是 **好下标**：

- `0 <= i < variables.length`
- `((aibi % 10)ci) % mi == target`

返回一个由 **好下标** 组成的数组，**顺序不限** 。

```cpp
class Solution {
public:
    int pow(long long  a, int n,int mod){
        int ans=1;
        while(n){
            if(n&1)ans=(ans*a)%mod;
            a=(a*a)%mod;
            n=n>>1;
        }
        return ans;
    }
    vector<int> getGoodIndices(vector<vector<int>>& variables, int target) {
        vector<int> ans;
        int cnt=0;
        for(auto &&i :variables){
            if(pow(pow(i[0],i[1],10),i[2],i[3])==target)ans.push_back(cnt);
            cnt++;
        }        
        return ans;
    }
};
```

# 二维前缀和

前缀和数组：
$$
sum[i+1][j+1]=arr[i][j]-sum[i][j]+sum[i+1][j]+sum[i][j+1]
$$
求以$[x_1,y_1]$为左上角，$[x_2,y_2]$为右下角的区域和：
$$
S=sum[x_2+1][y_2+1]+sum[x_1][y_1]-sum[x_1][y_2+1]-sum[x_2+1][y_1]
$$

## 例子

给定一个二维矩阵 `matrix`，以下类型的多个请求：

- 计算其子矩形范围内元素的总和，该子矩阵的 **左上角** 为 `(row1, col1)` ，**右下角** 为 `(row2, col2)` 。

实现 `NumMatrix` 类：

- `NumMatrix(int[][] matrix)` 给定整数矩阵 `matrix` 进行初始化
- `int sumRegion(int row1, int col1, int row2, int col2)` 返回 **左上角** `(row1, col1)` 、**右下角** `(row2, col2)` 所描述的子矩阵的元素 **总和** 。

## Code

```cpp
class NumMatrix {
public:
    vector<vector<int>> vt;
    NumMatrix(vector<vector<int>>& matrix) {
        int m=matrix.size();
        int n=matrix[0].size();
        vt.resize(m+1,vector<int>(n+1));
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                vt[i+1][j+1]=-vt[i][j]+vt[i+1][j]+vt[i][j+1]+matrix[i][j];
            }
        }
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        return vt[row2+1][col2+1]+vt[row1][col1]-vt[row2+1][col1]-vt[row1][col2+1];
    }
};
```

## 二维差分

![LC2132-c.png](https://pic.leetcode.cn/1702439895-HZofag-LC2132-c.png)



一般会和离散化结合，有点打脑壳。

原地累加更新时:

```cpp
//vector<vector<int>> dif(m+2,vector<int>(n+2));
 for (auto it : queries) {
            int x1 = it[0];
            int y1 = it[1];
            int x2 = it[2];
            int y2 = it[3];
     		//记录二维dif
            dif[x1+1][y1+1]++;
            dif[x1+1][y2 + 2]--;
            dif[x2 + 2][y1+1]--;
            dif[x2 + 2][y2 + 2]++;
        }
//进行原地复原时，类似于二维前缀和
for(int i=1;i<=m;i++){
    for(int j=1;j<=n;j++){
        dif[i][j]=dif[i][j]+dif[i-1][j]+dif[i][j-1]-dif[i-1][j-1];
    }
}
```

# 数位dp

大概就是一个1e9的范围，然后求满足xxx条件的数字有哪些。

用记忆化搜索来做。

考虑有可能正难则反。

1. 把n转为字符串
2. 根据n的位数来确定dp数组，具体情况具体考虑
3. 一般是dfs(int i,int cnt,bool is_lim,bool is_num)几个参数

如果求一个[a,b]，可以调用两次函数，或者在运行时多传一个参数，上下限同时传。

## 例子

给定一个正整数 `n` ，请你统计在 `[0, n]` 范围的非负整数中，有多少个整数的二进制表示中不存在 **连续的 1** 。

## code

```cpp
class Solution {
public:
    int findIntegers(int n) {
        int m = __lg(n);
        vector<vector<int>> memo(
            m + 1,
            vector<int>{
                -1,
                -1}); // 具体怎么写memo要根据题目来分析，同时记得完成初始化。
        auto f = [&](auto&& f, int i, bool pre1, bool is_lim) -> int {
            if (i < 0)
                return 1; // 边界条件，找到了一个数字。
            if (!is_lim && memo[i][pre1] != -1)//要在不受is_lim限制下，才用memo
                return memo[i][pre1];
            int up = is_lim ? n >> i & 1 : 1;
            //就两种情况，所以不用for循环，分开写就好，不然可以for循环跑一下。
            int res = f(f, i - 1, 0, is_lim && up == 0);
            if (!pre1 && up) {
                res += f(f, i - 1, 1, is_lim);
            }
            if (!is_lim)//记得更新memo
                memo[i][pre1] = res;
            return res;
        };
        return f(f, m, 0, 1);
    }
};
```

# 判断一个序列是否是另一个序列的子序列

```cpp
class Solution {
public:
    bool isSubsequence(string s, string t) {
        if(s.length()==0)return 1;
        int i=0;
        for(int c:t){
            if(s[i]==c&&++i==s.length()){
                return 1;
            }
        }

        return 0;
    }
};
```

