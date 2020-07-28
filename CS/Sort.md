# Sort

> 그간 공부했던 정렬 알고리즘에 대한 정리와 추가적인 알고리즘에 대한 정리

정렬 알고리즘은 정렬 방식에 따라 여러 방식의 알고리즘들이 존재한다. 알고리즘 종류만 따지더라도 `선택 정렬(Selection Sort)` 부터 `Bubble Sort`, `Quick Sort` 등 다양하게 있다. 


<br>


# Selection Sort (선택 정렬)

Selection Sort (선택 정렬)은 **제자리 정렬** 알고리즘 중 하나로 주어진 리스트 중 최소값을 찾고, 그 값을 맨 앞 앞에 위치한 값과 교체하는 방식으로 그 다음에는 앞 전에 교체한 위치를 뺀 나머지 리스트의 맨 앞에 위치와 교체한다.

<br>

## Process (Ascending)

1. 배열에서 최소값을 찾는다.
2. 찾은 최소값을 배열에서 가장 맨 앞에 위치한 값과 교체한다.
3. 교체한 맨 앞의 값을 제외한 배열에서 1번 과정부터 다시 반복

<br>

## Python Code

> 변수명 짓기 너무나 어려운 것..

**Code 1**

```python
def selection_sort(arr, ascending=True):
    for ix in range(len(arr)-1):
        _min = ix
        for jx in range(ix+1, len(arr)):
            if arr[jx] < arr[_min]:
                _min = jx
        arr[_min], arr[ix] = arr[ix], arr[_min]

    return arr
```



**Code 2**

```python
def selection_sort(data):
    for stand in range(len(data) - 1):
        lowest = stand
        for index in range(stand + 1, len(data)):
            if data[lowest] > data[index]:
                lowest = index
        data[lowest], data[stand] = data[stand], data[lowest]
    return data
```

<br>

## Big-O

시간 복잡도를 계산하면 첫 번째 반복문에서 `n` 만큼 돌 것이고, 두 번째 반복문에서는 첫 번째 루프를 지날 때마다 `n-1`, `n-2`, `...` 로 점차 감소할테지만, 그래도 `n`만큼 돌기 때문에 시간 복잡도는 `O(n^2)`다.

공간 복잡도는 배열 안에서만 모든 작업이 이루어지기 때문에 `O(n)` 이다.

<br>

# Bubble Sort (거품 정렬)

> 거품 정렬이 왜 거품 정렬일까라고 생각했는데, 원소의 이동이 마치 거품이 수면으로 올라오는 듯한 모습을 보인다고 해서 지어졌다고 한다. 궁금하면 [Bubble Sort - Wikipedia]([https://ko.wikipedia.org/wiki/%EA%B1%B0%ED%92%88_%EC%A0%95%EB%A0%AC](https://ko.wikipedia.org/wiki/거품_정렬))

거품 정렬은 Selection Sort와 유사하다고 볼 수 있는데, 그 이유는 리스트의 값을 하나 씩 순회하며 인접한 두 원소를 비교하고 자리를 교환하면서 정렬하는 알고리즘이다. 여기서 리스트를 순차적으로 순회한다는 점에서 어느 정도 유사한 면이 있다.

<br>

## Process (Ascending)

1. 첫 번째와 두 번째 원소를 비교, 그 후 두 번째와 세 번째 원소를 비교하는 방식으로 n 번째와 n+1 번째의 원소를 비교하여 이 중 조건에 의해 자리를 교환한다. 여기서 조건은 오름차순인지, 내림차순인지 이다.
2. (오름차순) 1번 과정을 거치면 가장 끝에 있는 원소의 값이 가장 크게 되어 다음 과정 진행할 때에는 마지막 위치를 제외하고 1번 과정을 반복한다.

<br>

## Python Code

**Code 1**

```python
def bubble_sort(arr, ascending=True):
    end = len(arr)
    while end > 0:
        for ix in range(end-1):
            if arr[ix] > arr[ix+1]:
                arr[ix], arr[ix+1] = arr[ix+1], arr[ix]
        end -= 1
    return arr
```

**Code 2**

```python
def bubble_sort(data):
    for index in range(len(data) - 1):
        swap = False
        for index2 in range(len(data) - index - 1):
            if data[index2] > data[index2 + 1]:
                data[index2], data[index2 + 1] = data[index2 + 1], data[index2]
                swap = True
        
        if swap == False:
            break
    return data
```

<br>

## Big-O

**시간 복잡도**는 반복문을 살펴보면 `(n-1) + (n-2) + ... + 2 + 1`로 점점 감소한다. 이는 `n(n-1)/2`으로 `O(n^2)`이다.

**공간 복잡도**는 배열 내부에서 위치를 바꾸는 방식이기 때문에 선택 정렬과 동일하게 `O(n)`이다.


<br>

# 삽입 정렬 (Insertion Sort)

삽입 정렬은 리스트의 모든 요소를 앞에서부터 차례대로 이미 정렬된 배열 부분과 비교하여, 자신의 위치를 찾아 삽입함으로써 정렬을 완성하는 알고리즘으로 선택 정렬과 유사하지만 더 효율적인 정렬 알고리즘으로 두 번째 인덱스부터 시작한다는 특이점이 있다. 

<br>

## Process

- 삽입 정렬은 버블 정렬과 달리 두 번째 인덱스부터 시작한다.
- 해당 인덱스(Key 값) 앞에 있는 데이터부터 비교하여 Key 값이 더 작으면 B 값을 뒤 인덱스로 복사 이를 Key값이 더 큰 데이터를 만날 때까지 반복, 그리고 큰 데이터를 만난 위치 바로 뒤에 Key 값을 이동


<br>


## Python Code

> 나는 처음 삽입 정렬이라길래 특정 리스트에 삽입해서 하는 줄 알았다. Code1은 처음에 짠 코드

**Code 1**

```python
def insertion_sort(arr, ascending=True):
    temp = [arr[0]]
    for idx in range(1, len(arr)):
        temp.append(arr[idx])
        for idx2 in range(idx, 0, -1):
            if temp[idx2] < temp[idx2-1]:
                temp[idx2], temp[idx2-1] = temp[idx2-1], temp[idx2]
            else:
                break
    return temp
```

**Code 2**

```python
def insertion_sort(data):
    for index in range(len(data) - 1):
        for index2 in range(index + 1, 0, -1):
            if data[index2] < data[index2 - 1]:
                data[index2], data[index2 - 1] = data[index2 - 1], data[index2]
            else:
              break
    return data
```

<br>

## Big-O

**시간 복잡도**는 최악의 경우 두 개의 반복문을 처음과 끝까지 돈다고 가정했을 때, 선택 정렬과 동일하게 `O(n^2)`이다. 효율적이라면서 선택 정렬과 동일하면 어쩌자는건가라고 생각할 수 있겠는데, 선택 정렬은 데이터가 어느 정도 정렬이 되어 있든 없든 항상 `O(n^2)`인데 반면에 삽입 정렬은 좌측에 정렬된 데이터가 어느 정도 순서를 유지하고 있기 때문에 중도에 멈추기도하여 `O(n^2)`보다는 항상 적은 시간 복잡도를 가진다.

**공간 복잡도**는 주어진 리스트에서 위치를 교환하기 때문에 `O(n)`이다. 하지만, **Code 1**의 경우에는 `temp`라는 리스트를 생성하여 그 안에서 정렬을 수행했기 때문에 `O(2n)`이다. (메모리를 2배나 차지한다.)


<br>

# QuickSort

**Quick Sort** (퀵 정렬)은 **불안정 정렬**에 속하며, 다른 원소와의 비교만으로 정렬을 수행하는 **비교 정렬**에 속한다. 여기서 **불안정 정렬**이란, **안정성** (stable) 이라는 걸 먼저 이해해야하는데, 쉽게 설명하면 기존의 순서를 유지하는 정렬을 말한다. "응? 순서를 바꾸는게 정렬인데 기존의 순서를 유지한다는게 뭐지?"라고 생각할 수 있는데, 여기서의 순서는 동일한 값의 순서를 의미한다. 개발을 하다보면 리스트의 모든 값이 `unique` 하지 않기 때문에 중복된 값이 존재하기 마련인데, 이 때 중복된 값들 사이의 순서를 유지하는 것을 말한다.

<br>

```python
arr = [5, 2, 3, 1, 4, 5, 8]
print(sorting(arr))

> [1, 2, 3, 4, 5, 5, 8]
# 여기서 두 개의 5는 순서를 유지해야한다. 앞에 있는 5는 맨 처음 가장 앞에 있던 5여야한다는 걸 의미한다.
```

약간 간단하게 예를 생각하면, 우리가 입장을 하기 위해 줄을 섰는데 입장하시려면 나이 순서대로 입장 가능하세요. 라고 안내가 나오고 나이 순서대로 순서를 정하는데, 이 때 맨 앞에 있던 사람은 동일한 나이를 가진 사람들 사이에서도 맨 앞에 있어야한다는 것과 똑같다. (순서가 바뀌면 얼마나 억울하겠는가)

또한, 퀵 정렬은 **분할 정복** 알고리즘의 하나로 평균적으로 매우 빠른 수행 속도를 자랑하는 정렬 방법을 이용한다. **합병 정렬** (merge sort)와 달리 리스트를 비균등하게 분할한다. 여기서 **분할 정복** (divide and conquer) 방법이란, 문제를 작은 2개의 문제로 분리하고 각각 해결한 다음, 결과를 모아서 원래의 문제를 해결하는 전략으로 대개 순환 호출을 이용하여 구현한다.

<br>

## Process

1. **피벗(pivot)** 선택한다. 여기서 피벗이란 리스트 안에 한 요소를 선택한다. (분할 하기 전 기준점을 의미)
2. **피벗**을 기준으로 피벗보다 작은 요소들은 모두 피벗의 왼쪽으로 옮겨지고 피벗보다 큰 요소들은 모두 피벗의 오른쪽으로 옮겨진다. (피벗을 중심으로 왼쪽 : 피벗의 작은 요소들, 오른쪽 : 피벗보다 큰 요소들)
3. 피벗을 제외한 왼쪽과 오른쪽 리스트를 정렬하는데, 분할된 부분에 리스트에 대해 순환 호출을 이용하여 정렬을 반복한다.
4. 부분 리스트들이 더 이상 분할이 불가능할 때까지 반복한다.



<br>

##  Python Code

**1.**

```python
def qsort(data):
    if len(data) <= 1:
        return data

    left, pivot, right = [], data[0], []

    for value in data[1:]:
        if value < data:
            left.append(value)
        else:
            right.append(value)

    return qsort(left) + [pivot] + qsort(right)
```

**2.**

```python
def qsort(data):
    if len(data) <= 1:
        return data

    center = len(data) // 2
    left, pivot, right = [], data[center], []

    for value in data[:center] + data[center+1:]:
        if value < pivot:
            left.append(value)
        else:
            right.append(value)

    return qsort(left) + [pivot] + qsort(right)
```



<br>

## Big-O

**시간 복잡도**는 **Code 1** 방식으로 진행하면, `O(n^2)`으로 계산된다. 그 이유는 초기 `pivot`을 중간이 아닌 맨 앞 원소로 잡아서 그런데, 이 경우에는 처음은 물론이고 파티션된 두 번째 정렬에서도 동일하게 맨 앞 원소부터 시작하는데, 시간 복잡도를 줄이기 위해서 **Code 2**에서는 가운데부터 시작하도록 수정하여 `O(n^2)` 보다 빠르게 한다. 이는 `O(nlogn)` 보다 더 빠르다고 알려져있다.

**공간 복잡도**는 순환 함수를 사용했지만, 배열의 공간 외에는 다른 공간을 더 크게 사용하진 않기 때문에 공간복잡도는 `O(n)` 이다.





<br>

# MergeSort

**MergeSort**은 병합 정렬이라 부르며, 분할 정복 방법을 사용하는 알고리즘이다. 여기서 분할 정복이 어떻게 쓰이냐면, 전체 데이터를 정확히 반으로 나누고 반으로 나눈 리스트로 또 반으로 나누어가는 과정을 반복하고 합치면서 정렬하는 알고리즘이다.

가장 큰 특징은 Stable Sort로 **안정 정렬**에 속한다. 이것도 코드 짜기 나름인 것 같지만, 분할 정복 알고리즘을 사용하기 때문에 리스트가 분할되어 다시 병합되는 과정을 거치면서 각 원소의 순서를 유지한 채로 정렬이 된다.



<br>

## Process

재귀 용법을 활용한 정렬 알고리즘이다.

1. 리스트를 절반으로 잘라 비슷한 크기의 두 부분 리스트로 나눈다.
2. 각 부분 리스트를 재귀적으로 합병 정렬을 이용해 정렬한다.
3. 두 부분 리스트를 다시 하나의 정렬된 리스트로 합병한다.



예시를 들어 과정을 보면 다음과 같음

1. `[1, 9, 3, 2]`

2. `[1, 9], [3, 2]` : 리스트를 절반으로 잘라 비슷한 크기의 두 부분 리스트로 나눈다.

3. `[1], [9], [3], [2]` : 동일하게 두 부분으로 나눈다.

4. `[1, 9], [3], [2]` : `[1]`과 `[9]`를 비교하면서 병합

5. `[1, 9], [2, 3]` : `[3]`과 `[2]`를 비교하여 정렬 후 병합

6. `[1, 2, 3, 9]` : `[1, 9]`와 `[2, 3]`을 비교하며 정렬하여 병합



<br>

## Python Code

**1.**

```python
def merge(left, right):
    merged = list()
    left_point, right_point = 0, 0
    
    while len(left) > left_point and len(right) > right_point:
        if left[left_point] > right[right_point]:
            merged.append(right[right_point])
            right_point += 1
        else:
            merged.append(left[left_point])
            left_point += 1

    while len(left) > left_point:
        merged.append(left[left_point])
        left_point += 1
        
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

**2.**

```python
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



<br>

## Big-O

**시간 복잡도**는 평균 `O(nlogn)` 로 계산되고 최선, 최악 모두 동일하다. **공간 복잡도**는 `O(n)`이다. 공간복잡도는 생각보다 쉽게 계산되었는데, 원래 리스트를 절반 씩 분할하며 정렬하더라도 원래의 사이즈인 `n` 이하로 분할되고 유지되기 때문에 공간 복잡도는 `O(n)`이다.





<br>

# HeapSort

완전 이진 트리를 기본으로 하는 힙(Heap) 자료 구조를 기반으로한 정렬 방식으로 여기서 완전 이진 트리란, 삽입할 때 왼쪽부터 차례대로 추가하는 이진 트리를 말한다. 힙 소트는 불안정 정렬에 속하는데 그 이유는 입력된 순서에 따라 왼쪽부터 차례대로 추가되지만, 순서가 바뀌게 되면서 자식 노드와 부모 노드의 위치로 인해 순서가 뒤죽박죽 된다.



<br>

## Process

1. 최대 힙을 구성한다.
2. 최대 힙을 구성하면 힙의 루트에는 가장 큰 값이 존재하는데, 루트의 값을 마지막 요소와 바꾼 후 힙의 사이즈를 하나 씩 줄여 나간다. (여기서 제거한 루트의 가장 큰 값을 추가해가면서 정렬하는 방식이다.)
3. 힙의 사이즈가 1보다 크면 위 과정 반복



<br>

## Python Code

**1.** python 내장 모듈인 heapq를 이용한 방법 

```python
import heapq

def heap_sort(data):
    heap = []
    for e in data:
        heapq.heappush(heap, e)
    result = []
    while heap:
        result.append(heapq.heappop(heap))
```



<br>

## Big-O

**시간복잡도**는 최선, 평균, 최악 모두 `O(nlogn)`이고, **공간복잡도**는 `O(n)`인 알고리즘에 속한다.


<br>


## Reference

* [퀵 정렬이란 - heejeong Kwon](https://gmlwjd9405.github.io/2018/05/10/algorithm-quick-sort.html)





