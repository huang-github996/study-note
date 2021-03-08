title: Leetcode41-缺失的第一个正整数
author: dogh
tags:
  - leetcode
  - sort
categories:
  - leetcode
date: 2021-01-27 22:51:00
---
question: 给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。
进阶：你可以实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案吗？

[leetcode-41](https://leetcode-cn.com/problems/first-missing-positive)
数据范围：
==$0 <= nums.length <= 300$==

==$-2^{31} <= nums[i] <= 2^{31} - 1$==

function - 1 : 解题思想，从数据范围来看，未出现正整数范围为 1 ~ 301 ；

	step1: 将所有非正整数转换为size之外的正整数，也就是不可能成为答案的数
	step2: 将所有在nums.size范围内的数，对应下标位置的值转换为负数，由于可能出现tmp - i 大于当前迭代器下标的可能，为避免后续访问时下标越界，在取tmp时需要取abs。
	step3: 遍历nums，将所有大于0的数所在位置的下标 + 1输出；
```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        for (auto &iter : nums) {
            if (iter <= 0) iter = n + 1;
        }
        for (auto &iter : nums) {
            int tmp  = abs(iter);
            if (abs(tmp) <= n) nums[tmp - 1] = -abs(nums[tmp - 1]);
        }
        for (int i = 0; i < nums.size(); i++) {
            if (nums[i] >= 0) return i + 1;
        }
        return nums.size() + 1;
    }

};
```
function - 2 ：

	step1： 遍历nums，使值和下标一一对应；
	step2：遍历nums，将第一个不对应的数字的下标输出，即为答案；
```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        for (int i = 0; i < n; i++) {
            while (nums[i] > 0 && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                swap(nums[nums[i] - 1], nums[i]);
            }
        }
        for (int i = 0; i < n; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return nums.size() + 1;
    }

};
```