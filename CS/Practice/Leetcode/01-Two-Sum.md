## 01. Two Sum

하나의 배열 (Array) 가 주어지고, 배열 안의 두 수를 합했을 때 타켓 (Target) 값과 일치하면, 두 수의 인덱스를 오름차순으로 리턴하는 문제이다. 

<br>

### Brute Force

* 시간 복잡도 : 반복문 두 개 사용으로 $$ O(n^2) $$
* 공간 복잡도 : 최초 입력 변수 외 index1, index2만 사용하기에 $$O(1)$$

```python
class Solution:
    def twoSum(self, nums: list, target: int):
        for index1 in range(len(nums)-1):
            for index2 in range(index1+1, len(nums)):
                if nums[index1] + nums[index2] == target:
                    return [index1, index2]
```

<br>

### One-pass Hash Table

* 시간 복잡도 : 반복문 1개 사용으로 $$O(n)$$
* 공간 복잡도 : 만약 Hash Table 사용할 경우 $$ O(n) $$ 인데, 여기서는 유사한 로직으로 수행 $$ O(1) $$ 

```python
class Solution:
    def twoSum(self, nums: list, target: int):
        for idx, n in enumerate(nums):
            remain = target - n
            if remain in nums[idx+1:]:
                return [idx, nums[idx+1:].index(remain)+idx+1]
```

