
## 0 만들기
> 정답은 맞췄다고 생각하는데, 프린트되어야하는 공식의 순서가 달라서 틀렸다고 나옴.

```python
import copy

def solution(n):
    integers = [str(i+1) for i in range(n)]
    computations = [' ', '+', '-']

    operations = ['1']
    for integer in integers[1:]:
        new_operations = []
        for computation in computations:
            for operation in operations:
                new_operations.append(operation + computation + integer)
        operations = copy.deepcopy(new_operations)
    
    for operation in operations:
        if eval(operation.replace(' ', '')) == 0:
            print(operation)
```

<br>

## 새
> 너무 간단하게 짜서 이게 맞는건지도 모호하다

```python
def solution(n):
    sec = 0
    k = 1
    while n > 0:
        sec += 1
        n -= k
        k += 1
        if n < k:
            k = 1
    return sec
```


<br>

## 트로피 진열
> 처음은 일단 지저분하지만 풀 수 있게끔 만들고, 이후 클린 코딩

```python
trophy_cnt = int(input())

trophies = []
for cnt in range(trophy_cnt):
    trophies.append(int(input()))

last_trophy, left = 0, []
for trophy in trophies:
    if trophy > last_trophy:
        left.append(trophy)
        last_trophy = trophy

last_trophy, right = 0, []
for trophy in trophies[::-1]:
    if trophy > last_trophy:
        right.append(trophy)
        last_trophy = trophy
```

<br>

## 성지키기
> x축과 y축에서 각 필요한 인원에 대해서 계산한다음에 최대값을 리턴했는데, 맞았다??

```python
n, m = [int(i) for i in input().split()]

m_guards = [1 for _m in range(m)]
n_guards = [1 for _n in range(n)]

for iy in range(n):
    for ix, cell in enumerate(input()):
        if cell == "X":
            m_guards[ix], n_guards[iy] = 0, 0

print(max(sum(m_guards), sum(n_guards)))
```



