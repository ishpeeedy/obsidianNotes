# Problem Statement
Given an array of integers `nums` and an integer `target`, return indices of the two numbers such that they add up to `target`.
You may assume that each input would have exactly one solution, and you may not use the same element twice.
You can return the answer in any order.
#Array #DSA #DSA-easy #Leetcode #HashMap #TwoPointer 
# Key Concepts
- **Hash Map**: Use an unordered map to store elements and their indices for O(1) lookup time.
- **Complement**: For each element `nums[i]`, calculate `complement = target - nums[i]` and check if it exists in the map.
- **Single Pass**: Traverse the array once, checking for complements and storing elements in the map.
# Implementation Steps
1. **Initialize a Hash Map**: Create an unordered map to store elements and their indices.
2. **Traverse the Array**:
   - For each element `nums[i]`, calculate `complement = target - nums[i]`.
   - Check if `complement` exists in the map:
     - If yes, return the indices `[mp[complement], i]`.
     - If no, store `nums[i]` and its index in the map.
3. **Return Empty Vector**: If no solution is found (though the problem guarantees one exists).
# Code Implementation
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> mp;
        for (int i = 0; i < nums.size(); i++) {
            int complement = target - nums[i];
            if (mp.find(complement) != mp.end()) {
                return {mp[complement], i};
            }
            mp[nums[i]] = i;
        }
        return {};
    }
};
```
# Sample Input/Output
### Input
```plaintext
nums = [2,7,11,15], target = 9
```
### Output
```plaintext
[0,1]
```
### Explanation
- Traverse the array:
  - `nums[0] = 2`: `complement = 9 - 2 = 7`, not in map → Store `{2: 0}`.
  - `nums[1] = 7`: `complement = 9 - 7 = 2`, found in map → Return `[0, 1]`.
### Input
```plaintext
nums = [3,2,4], target = 6
```
### Output
```plaintext
[1,2]
```
### Explanation
- Traverse the array:
  - `nums[0] = 3`: `complement = 6 - 3 = 3`, not in map → Store `{3: 0}`.
  - `nums[1] = 2`: `complement = 6 - 2 = 4`, not in map → Store `{2: 1}`.
  - `nums[2] = 4`: `complement = 6 - 4 = 2`, found in map → Return `[1, 2]`.
### Input
```plaintext
nums = [3,3], target = 6
```
### Output
```plaintext
[0,1]
```
### Explanation
- Traverse the array:
  - `nums[0] = 3`: `complement = 6 - 3 = 3`, not in map → Store `{3: 0}`.
  - `nums[1] = 3`: `complement = 6 - 3 = 3`, found in map → Return `[0, 1]`.
# Dry Run
### Input: nums = [2,7,11,15], target = 9
| Step | i | nums[i] | complement | Map State     | Action              |
| ---- | - | ------- | ---------- | ------------- | ------------------- |
| 1    | 0 | 2       | 7          | {}            | Store {2: 0}        |
| 2    | 1 | 7       | 2          | {2: 0}        | Found → Return [0,1]|
Final result: `[0, 1]`.
# Notes
- **Efficiency**:
  - Time Complexity: O(n), where `n` is the size of the array.
  - Space Complexity: O(n), for storing elements in the hash map.
- **Edge Cases**:
  - Array with two elements: Return their indices.
  - Array with duplicate elements: The solution handles this correctly by checking the complement before storing.
- **Follow-up**: This solution achieves O(n) time complexity, which is better than the brute-force O(n²) approach.