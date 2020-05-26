# 고급 정렬

<br>

## 1. 병합 정렬 (merge sort)

- 재귀용법을 활용한 정렬 알고리즘
    - 리스트를 절반으로 잘라 비슷한 크기의 두 부분 리스트로 나눈다.
    - 각 부분 리스트를 재귀적으로 합병 정렬을 이용해 정렬한다.
    - 두 부분 리스트를 다시 하나의 정렬된 리스트로 합병한다.

<br>

### 1. 알고리즘 이해

1. `[1, 9, 3, 2]`
2. `[1, 9], [3, 2]` : 리스트를 절반으로 잘라 비슷한 크기의 두 부분 리스트로 나눈다.
3. `[1], [9], [3], [2]` : 동일하게 두 부분으로 나눈다.
4. `[1, 9], [3], [2]` : `[1]`과 `[9]`를 비교하면서 병합
5. `[1, 9], [2, 3]` : `[3]`과 `[2]`를 비교하여 정렬 후 병합
6. `[1, 2, 3, 9]` : `[1, 9]`와 `[2, 3]`을 비교하며 정렬하여 병합

<br>

### 2. 알고리즘 구현

> ✔ 이 부분 내일 다시 한번 확인하자 !

```python
def merge(left, right):
    merged = list()
    left_point, right_point = 0, 0
    
    # case1 - left/right 둘다 있을때
    while len(left) > left_point and len(right) > right_point:
        if left[left_point] > right[right_point]:
            merged.append(right[right_point])
            right_point += 1
        else:
            merged.append(left[left_point])
            left_point += 1

    # case2 - left 데이터가 없을 때
    while len(left) > left_point:
        merged.append(left[left_point])
        left_point += 1
        
    # case3 - right 데이터가 없을 때
    while len(right) > right_point:
        merged.append(right[right_point])
        right_point += 1
    
    return merged

def mergesplit(data):
    if len(data) <= 1:
        return data
    medium = int(len(data) / 2)
    left = mergesplit(data[:medium])
    right = mergesplit(data[medium:])
    return merge(left, right)
```