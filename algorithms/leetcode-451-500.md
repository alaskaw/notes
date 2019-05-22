# LeetCode 451 - 500

### 451. Sort Characters By Frequency

Given a string, sort it in decreasing order based on the frequency of characters.‌

Example:

```
Input: "tree"
Output: "eert
```

Solution: sort + lambda, 一定要背

```c++
struct charfreq {
    int freqcnt = 0;
    int charpos;
    charfreq(int c): charpos(c) {}
};
string frequencySort(string s) {
    vector<charfreq> freq;
    for (int i = 0; i < 128; ++i) freq.push_back(charfreq(i));
    for (int i = 0; i < s.length(); ++i) ++freq[s[i]].freqcnt;
    sort(freq.begin(), freq.end(), [](const charfreq & a, const charfreq & b)-> bool {return a.freqcnt > b.freqcnt;});
    string output = "";
    for (int i = 0; i < 52; ++i) {
        int freqcnt = freq[i].freqcnt;
        char freqchar = freq[i].charpos;
        for (int j = 0; j < freqcnt; ++j) output += freqchar;
    }
    return output;

}
```

### 453. Minimum Moves to Equal Array Elements

Given a **non-empty** integer array of size n, find the minimum number of moves required to make all array elements equal, where a move is incrementing n - 1 elements by 1.

Example:

```
Input: [1,2,3]
Output: 3 ([1,2,3]  =>  [2,3,3]  =>  [3,4,3]  =>  [4,4,4])
```

Solution: 找到最大或最小值，累计差即可

```c++
int minMoves(vector<int>& nums) {
    int minval = nums[0], ret = 0;
    for (auto i: nums) minval = min(minval, i);
    for (auto i: nums) ret += i - minval;
    return ret;
}
```

### 454. 4Sum II

Given four lists A, B, C, D of integer values, compute how many tuples (i, j, k, l) there are such that A[i] + B[j] + C[k] + D[l] is zero.To make problem a bit easier, all A, B, C, D have same length of N where 0 ≤ N ≤ 500. All integers are in the range of -2^28 to 2^28 - 1 and the result is guaranteed to be at most 2^31 - 1.

Example:

```
Input: A = [1, 2], B = [-2,-1], C = [-1, 2], D = [ 0, 2]
Output: 2

Explanation:
The two tuples are:
1. (0, 0, 0, 1) -> A[0] + B[0] + C[0] + D[1] = 1 + (-2) + (-1) + 2 = 0
2. (1, 1, 0, 0) -> A[1] + B[1] + C[0] + D[0] = 2 + (-1) + (-1) + 0 = 0
```

Solution: 相当于2Sum的O(n^2)版本，对A和B的每项和做hashset，然后对C和D的每项和查找，一定要背

```c++
int fourSumCount(vector<int>& A, vector<int>& B, vector<int>& C, vector<int>& D) {
    unordered_map<int, int> abSum;
    for (auto a : A) for(auto b : B) ++abSum[a+b];
    int count = 0;
    for (auto c : C) for (auto d : D) count += abSum[0-c-d];
    return count;
}
```

### 456. 132 Pattern

Given a sequence of n integers a1, a2, ..., an, a 132 pattern is a subsequence ai, aj, ak such that i < j < k and ai < ak < aj. Design an algorithm that takes a list of n numbers as input and checks whether there is a 132 pattern in the list.

Example:

```
Input: [1, 3, 2]
Output: True
```

Solution: stack+逆序，逆序是为了保证i比j小

```c++
bool find132pattern(vector<int>& nums) {
    int s3 = INT_MIN;
    stack<int> st;
    for (int i = nums.size() - 1; i >= 0; --i){
        if (nums[i] < s3) return true;
        else while (!st.empty() && nums[i] > st.top()){ 
          s3 = st.top(); st.pop(); 
        }
        st.push(nums[i]);
    }
    return false;
}
```

### 459. Repeated Substring Pattern

Given a non-empty string check if it can be constructed by taking a substring of it and appending multiple copies of the substring together. You may assume the given string consists of lowercase English letters only and its length will not exceed 10000.‌

Example:

```
Input: "abcabcabcabc"
Output: True ("abc" * 3)
```

Solution: 正常遍历一遍即可，提前判断对长度i是否余0

```c++
bool repeatedSubstringPattern(string str) {
    int n = str.length(), half = n / 2;
    for (int i = 1; i <= half; ++i){
        if (n % i) continue;
        bool consist = true;
        string target = str.substr(0, i);
        for (int j = i; j < n && consist; j += i) {
            if (str.substr(j, i) != target) consist = false;
        }
        if (consist) return true;
    }
    return false;
}
```

### 461. Hamming Distance

The Hamming distance between two integers is the number of positions at which the corresponding bits are different. Given two integers x and y, calculate the Hamming distance. 0 ≤ x, y < 2^31.

Example:

```
Input: x = 1, y = 4
Output: 2, since
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
```

Solution: bit manipulation，一定要背

```c++
int hammingDistance(int x, int y) {
    int diff = x ^ y, dist = 0;
    while (diff) {
        dist += diff & 1;
        diff >>= 1;
    }
    return dist;
}
```

### 462. Minimum Moves to Equal Array Elements II

Given a **non-empty** integer array, find the minimum number of moves required to make all array elements equal, where a move is incrementing a selected element by 1 or decrementing a selected element by 1.

Example:

```
Input: [1,2,3]
Output: 2 ([1,2,3]  =>  [2,2,3]  =>  [2,2,2])
```

Solution: quick selection找中位数

```c++
int minMoves2(vector<int>& nums) {
    int n = nums.size();
    if (n < 2) return 0;
    if (n == 2) return abs(nums[0] - nums[1]); 
    int ret = 0, median = find_median(nums);
    for (auto i: nums) ret += abs(i - median);
    return ret;
}


int find_median(vector<int>& nums) {
    int l = 0, r = nums.size() - 1, target = (nums.size() - 1)/2;
    while (l < r) {
        int mid = quick_partition(nums, l, r);
        if (mid == target) return nums[mid];
        if (mid < target) l = mid + 1;
        else r = mid - 1;
    }
    return nums[l];
}


int quick_partition(vector<int>& nums, int l, int r) {
    int i = l + 1, j = r;
    while (true) {
        while (i < r && nums[i] <= nums[l]) ++i;
        while (l < j && nums[j] >= nums[l]) --j;
        if (i >= j) break;
        swap(nums[i], nums[j]);
    }
    swap(nums[l], nums[j]);
    return j;
}
```

### 474. Ones and Zeroes

In the computer world, use restricted resource you have to generate maximum benefit is what we always want to pursue. For now, suppose you are a dominator of m 0s and n 1s respectively. On the other hand, there is an array with strings consisting of only 0s and 1s. Now your task is to find the maximum number of strings that you can form with given m 0s and n 1s. Each 0 and 1 can be used at most once. The given numbers of 0s and 1s will both not exceed 100. The size of given string array won't exceed 600.

Example:

```
Input: Array = {"10","0001","111001","1","0"}, m = 5, n = 3
Output: 4 ({"10","0001","1","0")
```

Solution: dp, 对每个字符串遍历，dp[i][j]表示i个0和j个1能够构成的字符串个数。注意这道题dp需要从后往前，非常巧妙，一定要背和理解

```c++
int findMaxForm(vector<string>& strs, int m, int n) {
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
    for (auto str: strs) {
        vector<int> cnt = count(str);
        for (int i = m; i >= cnt[0]; --i)
            for (int j = n; j >= cnt[1]; --j)
                dp[i][j] = max(1 + dp[i-cnt[0]][j-cnt[1]], dp[i][j]);
    }
    return dp[m][n];
}
vector<int> count(string & s){
    vector<int> ans(2, 0);
    for(char c: s) ++ans[c-'0'];
    return ans;
}
```

### 475. Heaters

Given positions of houses and heaters on a horizontal line, find out minimum radius of heaters so that all houses could be covered by those heaters.‌

Example:

```
Input: [1,2,3,4], [1,4]
Output: 1 (heater placed in the position 1 and 4, use radius 1 standard)
```

Solution: 双指针遍历，注意要先sort一遍

```c++
int findRadius(vector<int>& houses, vector<int>& heaters) {
    int n = houses.size(), m = heaters.size();
    if (n == 0) return 0;
    sort(houses.begin(), houses.end());
    sort(heaters.begin(), heaters.end());
    int r = 0;
    for (int i = 0, j = 0; i < n; ++i) {
        while (j + 1 < m && abs(houses[i] - heaters[j + 1]) <= abs(houses[i] - heaters[j])) j++;
        r = max(r, abs(houses[i] - heaters[j]));
    }
    return r;
}
```

### 482. License Key Formatting

You are given a license key represented as a string S which consists only alphanumeric character and dashes. The string is separated into N+1 groups by N dashes.

Given a number K, we would want to reformat the strings such that each group contains exactly K characters, except for the first group which could be shorter than K, but still must contain at least one character. Furthermore, there must be a dash inserted between two groups and all lowercase letters should be converted to uppercase.

Example:

```
Input: S = "5F3Z-2e-9-w", K = 4
Output: "5F3Z-2E9W"
```

Solution: 正常处理

```c++
string licenseKeyFormatting(const std::string& S, const int& K) const{
    string str, result;
    for (char ch : S) {
        if (ch != '-') {
            if(ch > 96) ch -= 32; // to lowercase
            str += ch;
        }
    }
    int counter = 0, numberToPutInFirst = str.size() - K, i = 0;
    numberToPutInFirst %= K;


    while (i < numberToPutInFirst) result += str[i++];
    if (numberToPutInFirst < str.size() && !result.empty()) result += '-';
    while (i < str.size()) {
        result += str[i];
        if (++counter == K && i != str.size() - 1) {
            result += '-';
            counter = 0;
        }
        ++i;
    }
    return result;
}
```

### 485. Max Consecutive Ones

Given a binary array, find the maximum number of consecutive 1s in this array.‌

Example:

```
Input: [1,1,0,1,1,1]
Output: 3
```

Solution: 遍历一遍即可

```c++
int findMaxConsecutiveOnes(vector<int>& nums) {
    int global = 0, local = 0;
    for (auto i: nums) {
        if (!i) {
            global = max(global, local);
            local = 0;
        }
        else ++local;
    }
    global = max(global, local);
    return global;
}
```

### 491. Increasing Subsequences

Given an integer array, your task is to find all the different possible increasing subsequences of the given array, and the length of an increasing subsequence should be at least 2.

Example:

```
Input: [4, 6, 7, 7]
Output: [[4, 6], [4, 7], [4, 6, 7], [4, 6, 7, 7], [6, 7], [6, 7, 7], [7,7], [4,7,7]]‌
```

Solution: backtracking，注意这种题如果问个数则dp，问组合则backtracking。本题类似subset，每次dfs的一开始需要吧当前串放到结果里面

```c++
vector<vector<int>> findSubsequences(vector<int> &nums) {
    set<vector<int>> ret;
    vector<int> local;
    helper(nums, ret, local, 0, nums.size());
    return vector<vector<int>>(ret.begin(), ret.end());
}

void helper(vector<int> &nums, set<vector<int>> &ret, vector<int> &local, int start, int end) {
    if (local.size() >= 2) ret.insert(local);
    for (int i = start; i < end; ++i) {
        if (local.empty() || nums[i] >= local.back()){
            local.push_back(nums[i]);
            helper(nums, ret, local, i+1, end);
            local.pop_back();
        }
    }
}
```

### 494. Target Sum

You are given a list of non-negative integers, a1, a2, ..., an, and a target, S. Now you have 2 symbols + and -. For each integer, you should choose one from + and - as its new symbol. Find out how many ways to assign symbols to make sum of integers equal to target S.‌

Example:

```
Input: nums is [1, 1, 1, 1, 1], S is 3. 
Output: 5, since
-1+1+1+1+1 = 3,
+1-1+1+1+1 = 3,
+1+1-1+1+1 = 3,
+1+1+1-1+1 = 3,
+1+1+1+1-1 = 3.
```

Solution: DFS/backtrack指数次复杂度比较慢，可以用dp。原理如下，假设加的数字集合为P，减的数字集合为M，则sum(P) - sum(N) = target -> 2 * sum(P) = target + sum(nums)，因此我们只需要计算加法，新target为(旧target + sum(nums))/2。类似Q474, dp需要反着来，一定要背

```c++
int findTargetSumWays(vector<int>& nums, int s) {
    int sum = accumulate(nums.begin(), nums.end(), 0);
    return (sum < s || (s + sum) & 1)? 0: subsetSum(nums, (s + sum) >> 1);
}

int subsetSum(vector<int> & nums, int s){
    int dp[s+1] = {1};  // first = 1, others = 0
    for (int n : nums) for(int i = s; i >= n; --i) dp[i] += dp[i-n];
    return dp[s];
}
```