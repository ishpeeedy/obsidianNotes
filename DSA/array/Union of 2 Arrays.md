# Problem Statement
Given two sorted arrays, `arr1` and `arr2` of size `n` and `m`, find the union of the two arrays. The union of two arrays can be defined as the common and distinct elements in the two arrays. **Note**: The elements in the union should be in ascending order.
#Array #DSA #DSA-easy #TwoPointer 
# Key Concepts
- **Union**: The union of two arrays includes all distinct elements from both arrays.
- **Sorted Arrays**: Leverage the sorted property of the arrays to optimize the solution.
- **Three Approaches**:
  1. Using a map.
  2. Using a set.
  3. Using two pointers for optimal performance.
# Implementation Steps
### Approach 1: Using a Map
1. Use a map to store the frequency of elements from both arrays.
2. Traverse both arrays and insert their elements into the map.
3. Extract the keys from the map to form the union.
### Approach 2: Using a Set
1. Use a set to store distinct elements from both arrays.
2. Traverse both arrays and insert their elements into the set.
3. Extract the elements from the set to form the union.
### Approach 3: Using Two Pointers
1. Use two pointers, `i` and `j`, to traverse `arr1` and `arr2`.
2. Compare elements at `arr1[i]` and `arr2[j]`:
   - If `arr1[i] == arr2[j]`, add the element to the union and increment both pointers.
   - If `arr1[i] < arr2[j]`, add `arr1[i]` to the union and increment `i`.
   - If `arr1[i] > arr2[j]`, add `arr2[j]` to the union and increment `j`.
3. Add any remaining elements from `arr1` or `arr2` to the union.
# Code Implementation
### Approach 1: Using a Map
```cpp
#include <bits/stdc++.h>
using namespace std;
vector<int> FindUnion(int arr1[], int arr2[], int n, int m) {
    map<int, int> freq;
    vector<int> Union;
    for (int i = 0; i < n; i++) freq[arr1[i]]++;
    for (int i = 0; i < m; i++) freq[arr2[i]]++;
    for (auto &it : freq) Union.push_back(it.first);
    return Union;
}
int main() {
    int n = 10, m = 7;
    int arr1[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    int arr2[] = {2, 3, 4, 4, 5, 11, 12};
    vector<int> Union = FindUnion(arr1, arr2, n, m);
    cout << "Union of arr1 and arr2 is " << endl;
    for (auto &val : Union) cout << val << " ";
    return 0;
}
```
### Approach 2: Using a Set
```cpp
#include <bits/stdc++.h>
using namespace std;
vector<int> FindUnion(int arr1[], int arr2[], int n, int m) {
    set<int> s;
    vector<int> Union;
    for (int i = 0; i < n; i++) s.insert(arr1[i]);
    for (int i = 0; i < m; i++) s.insert(arr2[i]);
    for (auto &it : s) Union.push_back(it);
    return Union;
}
int main() {
    int n = 10, m = 7;
    int arr1[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    int arr2[] = {2, 3, 4, 4, 5, 11, 12};
    vector<int> Union = FindUnion(arr1, arr2, n, m);
    cout << "Union of arr1 and arr2 is " << endl;
    for (auto &val : Union) cout << val << " ";
    return 0;
}
```
### Approach 3: Using Two Pointers
```cpp
#include <bits/stdc++.h>
using namespace std;
vector<int> FindUnion(int arr1[], int arr2[], int n, int m) {
    int i = 0, j = 0;
    vector<int> Union;
    while (i < n && j < m) {
        if (arr1[i] <= arr2[j]) {
            if (Union.size() == 0 || Union.back() != arr1[i]) Union.push_back(arr1[i]);
            i++;
        } else {
            if (Union.size() == 0 || Union.back() != arr2[j]) Union.push_back(arr2[j]);
            j++;
        }
    }
    while (i < n) {
        if (Union.back() != arr1[i]) Union.push_back(arr1[i]);
        i++;
    }
    while (j < m) {
        if (Union.back() != arr2[j]) Union.push_back(arr2[j]);
        j++;
    }
    return Union;
}
int main() {
    int n = 10, m = 7;
    int arr1[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    int arr2[] = {2, 3, 4, 4, 5, 11, 12};
    vector<int> Union = FindUnion(arr1, arr2, n, m);
    cout << "Union of arr1 and arr2 is " << endl;
    for (auto &val : Union) cout << val << " ";
    return 0;
}
```
# Sample Input/Output
### Input
```plaintext
arr1 = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
arr2 = [2, 3, 4, 4, 5, 11, 12]
```
### Output
```plaintext
Union of arr1 and arr2 is
1 2 3 4 5 6 7 8 9 10 11 12
```
# Notes
- **Efficiency**:
  - Approach 1 (Map): Time Complexity: O((m + n) * log(m + n)), Space Complexity: O(m + n).
  - Approach 2 (Set): Time Complexity: O((m + n) * log(m + n)), Space Complexity: O(m + n).
  - Approach 3 (Two Pointers): Time Complexity: O(m + n), Space Complexity: O(m + n).
- **Edge Cases**:
  - One array is empty: Return the other array.
  - Both arrays are empty: Return an empty array.
  - Arrays with all distinct elements: Return the concatenation of both arrays.