# Search

## 순차 탐색 (Sequential Search)

- 탐색은 여러 데이터 중에서 원하는 데이터를 찾아내는 것을 의미하는데, 이 때 순차탐색란, 데이터가 담겨있는 리스트를 앞에서부터 하나씩 비교해서 원하는 데이터를 찾는 방법을 말한다.

```python
def sequential_search(data, search_value):
    for index, value in enumerate(data):
        if value == search_value:
            return index
    return -1
```

<br>

## 이진 탐색 (Binary Search)

> ✔  기본 가정이 정렬된 데이터라는 점 기억하자 !

- 탐색할 자료를 둘로 나누어 해당 데이터의 위치를 예상하며 탐색하는 방법

<br>

### 분할 정복 알고리즘과 이진 탐색

> ✔ 둘로 나누는 걸로 봐서 분할 정복 알고리즘과 연관이 높다고 생각되며, 알고리즘은 n → logn → .. 식으로 복잡도가 감소하는 것과 유사해보임

- 분할 정복 알고리즘 (Divide and Conquer)
    - Divide : 문제를 하나 또는 둘 이상으로 나눈다.
    - Conquer : 나눠진 문제가 기존 문제보다 충분히 작고, 해결이 가능하다면 해결하고 그렇지 않다면 반복한다.
- 이진 탐색
    - Divide : 리스트를 두 개의 서브 리스트로 나눈다.
    - Conquer
        - Search Value > Center Value : 뒷 부분 서브 리스트에서 탐색
        - Search Value < Center Value : 앞 부분 서브 리스트에서 탐색

<br>

### 알고리즘 구현

```python
# my code
def binary_search(data, search_value):
    if data is False:
        return False
    center = int(len(data) / 2)
    if data[center] == search_value:
        return True
    else:
        if data[center] < search_value:
            return binary_search(data[center+1:], search_value)
        else:
            return binary_search(data[:center], search_value)
```

```python
# study code
def binary_search(data, search):
    print (data)
    if len(data) == 1 and search == data[0]:
        return True
    if len(data) == 1 and search != data[0]:
        return False
    if len(data) == 0:
        return False
    
    medium = len(data) // 2
    if search == data[medium]:
        return True
    else:
        if search > data[medium]:
            return binary_search(data[medium+1:], search)
        else:
            return binary_search(data[:medium], search)
```