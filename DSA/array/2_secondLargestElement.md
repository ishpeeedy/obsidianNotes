# Problem Statement
Given an array of integers `nums`, return the second-largest element in the array. If the second-largest element does not exist, return `-1`.
#array #DSA #DSA-easy
# Key Concepts
- **Array Traversal**: Iterating through the array to examine each element.
- **Sorting or Comparison Logic**: Identifying the largest and second-largest values efficiently.
- **Edge Cases**: Handling scenarios such as arrays with all identical elements or arrays with fewer than two elements.
# Implementation Steps
1. **Check Array Size**: If the array has fewer than two elements, return `-1` immediately.
2. **Initialize Variables**: Use two variables to track the largest and second-largest values, initializing them appropriately.
3. **Iterate Through the Array**: Traverse the array to update the largest and second-largest values based on comparisons.
4. **Return Result**: After traversal, return the second-largest value or `-1` if it remains unset.
# Code Implementation
```cpp
#include <iostream>
#include <vector>
#include <limits.h>
using namespace std;
int secondLargest(vector<int>& nums) {
    if (nums.size() < 2) return -1;
    int largest = INT_MIN, secondLargest = INT_MIN;
    for (int num : nums) {
        if (num > largest) {
            secondLargest = largest;
            largest = num;
        } else if (num > secondLargest && num < largest) {
            secondLargest = num;
        }
    }
    return (secondLargest == INT_MIN) ? -1 : secondLargest;
}
int main() {
    vector<int> nums1 = {8, 8, 7, 6, 5};
    cout << "Second Largest: " << secondLargest(nums1) << endl;
    vector<int> nums2 = {10, 10, 10, 10, 10};
    cout << "Second Largest: " << secondLargest(nums2) << endl;
    return 0;
}
```
# Sample Input/Output
### Input
```plaintext
nums = [8, 8, 7, 6, 5]
```
### Output
```plaintext
Second Largest: 7
```
### Explanation
The largest value in `nums` is `8`, the second largest is `7`.
### Input
```plaintext
nums = [10, 10, 10, 10, 10]
```
### Output
```plaintext
Second Largest: -1
```
### Explanation
The only value in `nums` is `10`, so there is no second largest value, thus `-1` is returned.
# Dry Run
### Input: nums = [8, 8, 7, 6, 5]
1. Initialize `largest = INT_MIN`, `secondLargest = INT_MIN`.
2. Traverse the array:
   - `num = 8`: Update `largest = 8`, `secondLargest = INT_MIN`.
   - `num = 8`: No change.
   - `num = 7`: Update `secondLargest = 7`.
   - `num = 6`: No change.
   - `num = 5`: No change.
3. Return `secondLargest = 7`.
### Input: nums = [10, 10, 10, 10, 10]
1. Initialize `largest = INT_MIN`, `secondLargest = INT_MIN`.
2. Traverse the array:
   - `num = 10`: Update `largest = 10`, `secondLargest = INT_MIN`.
   - `num = 10`: No change.
   - `num = 10`: No change.
   - `num = 10`: No change.
   - `num = 10`: No change.
3. Return `secondLargest = -1`.
# Notes
- **Efficiency**: The algorithm runs in O(n) time, making it optimal for this problem.
- **Edge Cases**: Ensure proper handling of arrays with fewer than two elements or arrays with all identical elements.
- **Extensions**: Modify the program to handle arrays of different data types or find the third-largest element as an additional feature.