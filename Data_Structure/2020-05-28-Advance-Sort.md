# 고급 정렬

## 1. 병합 정렬 (merge sort)

- 재귀용법을 활용한 정렬 알고리즘
    - 리스트를 절반으로 잘라 비슷한 크기의 두 부분 리스트로 나눈다.
    - 각 부분 리스트를 재귀적으로 합병 정렬을 이용해 정렬한다.
    - 두 부분 리스트를 다시 하나의 정렬된 리스트로 합병한다.

<br>

## 알고리즘 구현

> ✔ 내가 이해한 것과 공부할 때 본 코드의 순서가 다름. 내 코드는 병합 후에 정렬을 진행했고, 공부할 때 본 코드는 정렬하며 병합 (조금 더 살펴보자 !)

1. `[1, 9, 3, 2]`
2. `[1, 9], [3, 2]` : 리스트를 절반으로 잘라 비슷한 크기의 두 부분 리스트로 나눈다.
3. `[1], [9], [3], [2]` : 동일하게 두 부분으로 나눈다.
4. `[1, 9], [3], [2]` : `[1]`과 `[9]`를 비교하면서 병합
5. `[1, 9], [2, 3]` : `[3]`과 `[2]`를 비교하여 정렬 후 병합
6. `[1, 2, 3, 9]` : `[1, 9]`와 `[2, 3]`을 비교하며 정렬하여 병합


```python
# my code
def merge_sort(data):
    if len(data) <= 1:
        return data
    center = int(len(data) / 2)
    left, right = merge_sort(data[:center]), merge_sort(data[center:])
    return sort(left, right)

def sort(left, right):
    result = []
    if left is not None:
        result += left
    if right is not None:
        result += right

    for ix1 in range(len(result)-1):
        minimum = ix1
        for ix2 in range(ix1+1, len(result)):
            if result[ix2] < result[minimum]:
                minimum = ix2
        result[ix1], result[minimum] = result[minimum], result[ix1]

    return result
```
```python
# study code
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

<br>

## 2. 퀵 정렬 (quick sort)

- **정렬 알고리즘의 꽃(🌺)**
- 기준점(Pivot)을 정하여, 기준점보다 작은 데이터는 왼쪽, 큰 데이터는 오른쪽으로 모으는 함수
- 각 왼쪽과 오른쪽은 재귀용법을 사용하여, 다시 동일 함수를 호출하여 반복
- `return left + pivot + right`


<br>

## 알고리즘 구현
> ✔ 오히려 병합 정렬보다 간단하지만 생각하는데 오래 걸렸음. 오히려 복잡하게 생각하지 말고 단순하게 생각하자 !

- quicksort 함수
    - 리스트의 길이가 1이면 바로 리턴
    - 그렇지 않다면, 리스트 맨 앞 데이터를 기준점(Pivot)으로 놓고 left, right 리스트 변수 생성
    - 맨 앞에 데이터를 뺀 나머지 데이터를 기준점과 비교(Pivot)
        - 기준점보다 작으면 left에 추가, 크면 right에 추가
    - `return quicksort(left) + pivot + quicksort(right)`

```python
# my code
def quick_sort(data):
    if len(data) <= 1:
        return data
    
    pivot = data[0]
    left, right = [], []
    # merge sort와 유사

    for ix in range(1, len(data)):
        val = data[ix]
        if val < pivot:
            left.append(val)
        else:
            right.append(val)

    return quick_sort(left) + [pivot] + quick_sort(right)
```

```python
# study code
def qsort(data):
    if len(data) <= 1:
        return data
    
    left, right = list(), list()
    pivot = data[0]
    
    for index in range(1, len(data)):
        if pivot > data[index]:
            left.append(data[index])
        else:
            right.append(data[index])
    
    return qsort(left) + [pivot] + qsort(right)
```