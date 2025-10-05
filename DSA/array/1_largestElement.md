# Problem Statement
Given an array of integers, find and return the largest element in the array.
#array #DSA #DSA-easy 
# Key Concepts
- **Array Traversal**: Iterating through the array to examine each element.
- **Comparison Logic**: Comparing elements to determine the largest value.
- **Edge Cases**: Handling scenarios such ass empty arrays or arrays with all identical elements.
# Implementation Steps
1. **Initialize a Variable**: Start with a variable to hold the maximum value, typically initialized to the first element of the array.
2. **Iterate Through the Array**: Use a loop to traverse the array.
3. **Update Maximum**: Compare each element with the current maximum and update if the element is larger.
4. **Return/Print the Result**: Output the largest element after completing the traversal.
# Code Implementation
```cpp
#include <iostream>
#include <vector>
using namespace std;
int largestElement(vector<int>& nums) {
    if (nums.empty()) return -1;
    int largest = nums[0];
    for (int num : nums) {
        if (num > largest) {
            largest = num;
        }
    }
    return largest;
}
int main() {
    vector<int> nums = {3, 5, 7, 2, 8};
    cout << "Largest Element: " << largestElement(nums) << endl;
    return 0;
}
```
# Sample Input/Output
### Input
```plaintext
nums = [3, 5, 7, 2, 8]
```
### Output
```plaintext
Largest Element: 8
```
### Explanation
- Start with `max = nums[0] = 3`.
- Compare `max` with each element:
  - `5 > 3` → Update `max = 5`
  - `7 > 5` → Update `max = 7`
  - `2 < 7` → No change
  - `8 > 7` → Update `max = 8`
- Final result: `8`.
# Dry Run
### Input: nums = [3, 5, 7, 2, 8]
1. Initialize `largest = nums[0] = 3`.
2. Traverse the array:
   - `num = 5`: Update `largest = 5`.
   - `num = 7`: Update `largest = 7`.
   - `num = 2`: No change.
   - `num = 8`: Update `largest = 8`.
3. Return `largest = 8`.
# Notes
- **Efficiency**: The algorithm runs in O(n) time, making it optimal for this problem.
- **Edge Cases**: Ensure proper handling of empty arrays.
- **Extensions**: Modify the program to handle arrays of different data types or find the second-largest element as an additional feature.