## Q. 스택, 그리디 - 스택 수열
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

<br>

## Q. 배열, 완전 탐색 - 블랙잭

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

## Q. 배열, 구현 - 음계

```python
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

## Q. 기능 개발
> ✔ 최대한 Stack과 Queue를 이용하려고 노력함
```python
import math
import queue

def solution(progresses, speeds):
    q = queue.Queue()
    
    for progress, speed in zip(progresses, speeds):
        duration = math.ceil((100 - progress) / speed)
        q.put(duration)
        
    answers = []
    duration, answer = q.get(), 1
    while q.qsize():
        work = q.get()
        if work <= duration:
            answer += 1
        else:
            answers.append(answer)
            duration, answer = work, 1
    answers.append(answer)
    return answers
```
```python
import math

def solution(progresses, speeds):
    answer = []

    remain_times = []
    for progress, speed in zip(progresses, speeds):
        remain_times.append(math.ceil((100 - progress) / speed))

    deploy, prev_date = 1, remain_times[0]
    for date in remain_times[1:]:
        if date <= prev_date:
            deploy += 1
        else:
            answer.append(deploy)
            deploy = 1
            prev_date = date
    answer.append(deploy)

    return answer
```

<br>

## Q. 탑
> ✔ Stack, List만 이용하면되어서, Queue까진 사용하지 않았다.

```python
def solution(heights):
    answers = []
    for ix, height in enumerate(heights):
        answer = 0
        for tower_ix, prev_height in enumerate(heights[:ix]):
            if prev_height > height:
                answer = tower_ix + 1
        answers.append(answer)
    return answers
```

<br>

## Q. 주식 가격
```python
def solution(prices):
    answer = []
    for ix, price in enumerate(prices):
        seconds = 0
        for next_price in prices[ix:]:
            seconds += 1
            if next_price < price:
                break
        answer.append(seconds-1)
                
    return answer
```

<br>

## Q. 2 X n 타일링
> 다시 풀어야함
```python
def solution(n):
    combinations = [[0, '']]
    
    answer, answers = 0, []
    for i in range(n):
        new_combinations = []
        for block_length, block in [[1, '1'], [2, '2']]:
            for length, combination in combinations:
                new_length = length + block_length
                new_combination = combination + block
                if new_length == n:
                    if new_combination not in answers:
                        answers.append(new_combination)
                        answer += 1
                if new_length < n:
                    new_combinations.append([new_length, new_combination])
        combinations = new_combinations
    return answer % 1000000007
```

<br>

## Q. 이중우선순위큐
> 아무리 아무리 해도 heap이 제대로 정렬되지 않은 경우가 있어 많이 애먹음. 해결 방법이 python에서의 heapq는 최소 힙이기 때문에 정렬을 위해서는 우선적으로 1회 pop이 필요했음 단순히 넣기만한다고 정렬되는 것이 아니라 pop으로 최소값을 가져온 이후에 나머지 노드의 값을 정렬하는데, 이 과정을 거쳐야지만 정렬된다.

```python
import heapq

def solution(operations):
    heap = []
    for operation in operations:
        heapq.heapify(heap)
        command, number = operation.split()
        if command == "I":
            heap.append(int(number))
        elif heap and command == "D":
            if number == "1":
                heap.pop()
            elif number == "-1":
                heap.pop(0)
    heapq.heapify(heap)
    if heap:
        _min = heapq.heappop(heap)
        return [heap.pop(), _min]
    return [0, 0]
```
