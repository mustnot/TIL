# 기본 정렬

> ✔ 다양한 정렬 알고리즘을 통해 이 후 알고리즘 성능 분석에 대해서도 이해할 수 있음

- 정렬 (Sorting) : 어떤 데이터들이 주어졌을 때 이를 정해진 순서대로 나열하는 것
- 정렬은 프로그램 작성시 빈번하게 필요하며, **알고리즘 학습의 필수**이다.

<br>

## 1. 버블 정렬 (Bubble Sort)

- 두 인접한 데이터를 비교해서, 앞에 있는 데이터가 뒤에 있는 데이터보다 크면, 자리를 바꾸는 알고리즘

```python
# code 1 : my code
def bubble_sort(data):
    while True:
        swap = 0
        for ix in range(len(data)-1):
            if data[ix] > data[ix+1]:
                data[ix], data[ix+1] = data[ix+1], data[ix]
                swap += 1
        if swap == 0:
            break
    return data
```
```python
# code 2 : study code
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

## 2. 삽입 정렬 (Insertion Sort)

- 삽입 정렬은 버블 정렬과 달리 두 번째 인덱스부터 시작한다.
- 해당 인덱스(Key 값) 앞에 있는 데이터부터 비교하여 Key 값이 더 작으면 B 값을 뒤 인덱스로 복사 이를 Key값이 더 큰 데이터를 만날 때까지 반복, 그리고 큰 데이터를 만난 위치 바로 뒤에 Key 값을 이동

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

## 3. 선택 정렬 (Selection Sort)

- 주어진 데이터 중 최소값을 찾아 데이터 맨 앞에 위치한 값과 교체함
- 맨 앞의 위치를 뺀 나머지 데이터를 동일한 방법으로 반복

```python
# code 1 : my code
def selection_sort(data):
    for ix1 in range(len(data)-1):
        min_index = ix1
        for ix2 in range(ix1+1, len(data)):
            if data[ix2] < data[idx]:
                min_index = ix2
        data[ix1], data[min_index] = data[min_index], data[ix1]
    return data	
```

```python
# code 2 : study code
def selection_sort(data):
    for stand in range(len(data) - 1):
        lowest = stand
        for index in range(stand + 1, len(data)):
            if data[lowest] > data[index]:
                lowest = index
        data[lowest], data[stand] = data[stand], data[lowest]
    return data
```