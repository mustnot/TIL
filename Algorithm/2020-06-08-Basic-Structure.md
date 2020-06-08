## Basic Structure Algorithm Practice

### 배열, 구현 - 음계 - 난이도 하

```python
# my code
def solution(array):
    ascending, descending = False, False
    for ix in range(1, len(array)):
        if array[ix-1] < array[ix]:
            ascending = True
        elif array[ix-1] > array[ix]:
            descending = True

    if ascending and descending:
        return 'mixed'
    elif ascending:
        return 'ascending'
    else:
        return 'descending'
        
array = [int(e) for e in str(input()).split()]

print(solution(array))
```

<br>

### 배열, 완전 탐색 - 블랙잭 - 난이도 하

```python
def solution(n, m, cards):
    hap = 0
    for i in range(n-2):
        for j in range(i+1, n-1):
            for k in range(j+1, n):
                new_hap = cards[i] + cards[j] + cards[k]
                if hap < new_hap <= m:
                    hap = new_hap
    return hap

n, m = list(map(int, input().split()))
cards = list(map(int, input().split()))

print(solution(n, m, cards))
```

<br>

### 스택, 그리디 - 스택 수열 - 난이도 하

```python
def solution(values):
    cnt = 1
    array, result = [], []

    for value in values:
        while cnt <= value:
            stack.append(cnt)
            print('+')
        if stack[-1] == data:
            stack.pop()
            print('-')
        else:
            print('NO')
            return 0
    return 1

n = int(input())

values = []
for i in range(n):
    values.append(int(input()))

solution(values)
```