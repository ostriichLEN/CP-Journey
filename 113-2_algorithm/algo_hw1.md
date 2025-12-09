# 演算法HW1

## 1. 本週影片中提到的NP-complete問題有哪些？
- 0/1 Knapsack problem
- Traveling salesperson problem 
- Partition problem
- Art gallery problem

## 2. 請上網找一個老師沒說過的NP-complete問題，並舉個例子說明該問題的輸入與輸出。
- Boolean Satisfiability Problem
> - 給定一個布林公式，是否有一組數值能使方程式為真?

舉例: 輸入: `A or (B and C)`
由於我們知道當`B=C=1`時該布林方程式為真，故這組數值可以是`(A,B,C)=(0,1,1)`或是`(A,B,C)=(1,1,1)`
所以對於`A or (B and C)`這筆輸入而言，的確有一組(含)以上的數值使其為真。
因此輸出為:True (有一組(含)以上的數值使輸入之方程式為真)



## 3. 二元搜尋應用：當數字可以重覆
花費時間:20min
完成程度:完全自己解出
### 解題思路
一般的二元搜尋法若找到答案則會立刻回傳`mid`，這種方法能用最短步驟找到目標值。
但本題要求回傳最小索引值的答案，因此我將算法設計成如果找到答案不直接回傳，而是將右指標改成`mid-1`，並記錄當前的`mid`為可能的正確答案。這種寫法會篩選掉所有比`a[mid]`更大的結果，包含符合搜尋條件的值與其他值。最終當迴圈條件不滿足跳出迴圈時，`binSrh`會回傳最後一個紀錄下來的`mid`，即為最小索引值的目標。

如果我們要找最大索引值的目標可以透過部分修改完成，只要在搜尋到目標時改為變更左指標為`mid+1`並記錄`mid`，修該後儲存的最後一筆`mid`即是最大索引值的目標。

#### 找最大的版本
```cpp=
#include <bits/stdc++.h>
using namespace std;

int binSrh(int l,int r,int a[],int t){
	int ans=-1;
	while(l<=r){
		int mid= l + (r-l) / 2;
		if (a[mid]==t){
			l=mid+1;
			ans=mid;
		}
		else if (t > a[mid]){
			l=mid+1;
		}
		else{
			r=mid-1;
		}
	}
	return ans;
}
int main(){
	int n;
	cin>>n;
	int a[n];
	for (int i=0;i<n;i++){
		cin>>a[i];
	}
	sort(a,a+n);
	int t;
	cin>>t;
	cout << binSrh(0,n-1,a,t);
}

```

![image](https://hackmd.io/_uploads/ryHs4Crckg.png)


### 程式碼
```cpp=
#include <bits/stdc++.h>
using namespace std;

int binSrh(int l,int r,int a[],int t){
	int ans=-1;
	while(l<=r){
		int mid= l + (r-l) / 2;
		if (a[mid]==t){
			r=mid-1;
			ans=mid;
		}
		else if (t > a[mid]){
			l=mid+1;
		}
		else{
			r=mid-1;
		}
	}
	return ans;
}
int main(){
	int n;
	cin>>n;
	int a[n];
	for (int i=0;i<n;i++){
		cin>>a[i];
	}
	sort(a,a+n);
	int t;
	cin>>t;
	cout << binSrh(0,n-1,a,t);
}
```
### input/output
![image](https://hackmd.io/_uploads/SkJgf0S9Jg.png)



## 4. 二元搜尋應用：找數字該插入的位置
花費時間：15min
完成程度：完全自己解出
### 解題思路
這題我先寫了一個一般的Binary Search從`nums`搜尋`target`，並且將`ans`預設為`-1`，如此一來如果在nums內搜尋不到`taget`則`ans`將維持預設值`-1`。
接著如果成功搜尋到我就直接回傳`ans`，否則將`target`放入`nums`接著排序，再進行一次Binary Search。由於第二次Binary Search能保證`target`一定在`nums`內，所以直接回傳`mid`即為答案。
### 程式碼
```cpp=
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int l=0,r=nums.size()-1;
        int ans=-1;
        while(l<=r){
            int mid=l+(r-l)/2;
            if (nums[mid]==target){
                ans=mid;
                break;
            }
            else if (target>nums[mid]){
                l=mid+1;
            }
            else {
                r=mid-1;
            }
        }
        if (ans==-1){
            nums.push_back(target);
            sort(nums.begin(),nums.end());
            l=0;
            r=nums.size()-1;
            while(l<=r){
                int mid=l+(r-l)/2;
                if (nums[mid]==target){
                    return mid;
                }
                else if (target>nums[mid]){
                    l=mid+1;
                }
                else {
                    r=mid-1;
                }
            }
        }
        return ans;
    }
};
```
### input/output

![截圖 2025-02-21 下午3.55.13](https://hackmd.io/_uploads/HJJOZhSqyl.png)

## 5. 二元搜尋應用：找到最早出問題的版本
花費時間：30min
完成程度：完全自己解出
### 解題思路
這題一開始不容易理解題意，我看了範例測資的解釋才意識到是Binary Search的應用。我在這題設計了一個二分搜尋法程式，並用題目提供的函數 `isBadVersion()`來判斷`n`是否為錯誤版本。
我在解這題時遇到最大的困難是上限溢位的問題，參考老師影片將`int mid = (l+r)/2`改為`int mid = l+(r-l)/2`後就能順利解決。
這題比較特別的點是搜尋目標除了要是錯誤版本之外，同時也要是第一個錯誤版本，因此我在決定回傳答案時是回傳左指標l而非通常實作Binary Search拾回傳的mid。
### 程式碼
```cpp=
class Solution {
public:
    int firstBadVersion(int n) {
        int l=1, r=n;
        while (l<r) { 
            int mid=l+(r-l)/2;
            if (isBadVersion(mid)) {
                r=mid; 
            } 
            else {
                l=mid+1; 
            }
        }
        return l;
    }
};

```
### input/output
![截圖 2025-02-21 下午3.27.56](https://hackmd.io/_uploads/B1s-osHcye.png)


## 6. 二元搜尋應用：開根號後的整數部分
花費時間:30min
完成程度:部分求助chatGPT
### 解題思路
這題我一開始是想透過二分搜尋的Worse Case並計算次數來找到`sqrt(x)`，實作後才發現我記錯二分搜尋的時間複雜度了。
後來我才發現我把問題想得太複雜了，其實只需要在1~x的範圍搜尋滿足`i*i<x` 條件的最大i即是答案。
在實作程式時我遇到了`mid * mid`太大導致上限溢位的問題而求助chatGPT，為了避免`mid*mid`導致上限溢位，改用`mid <= x / mid`取代`mid * mid <= x`。
### 程式碼
```cpp=
class Solution {
public:
    int mySqrt(int x) {
        if (x == 0 || x == 1) return x;

        int l = 1, r = x, ans = 0;

        while (l <= r) { 
            int mid = l + (r - l) / 2;

            if (mid <= x / mid) { 
                ans = mid;  
                l = mid + 1; 
            } 
            else {
                r = mid - 1; 
            }
        }

        return ans; 
    }
};

```

### input/output

![image](https://hackmd.io/_uploads/S1FsRCr9Jg.png)
