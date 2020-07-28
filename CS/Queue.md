# Queue
- 줄을 서는 행위와 유사
- 가장 먼저 넣은 데이터를 가장 먼저 꺼낼 수 있는 구조
    - FIFO(First-In, First-Out) 또는 LILO(Last-In, Last-Out) 방식으로 스택과 꺼내는 순서가 반대
- 어디에 Queue가 많이 쓰일까?
    - 멀티 태스킹을 위한 프로세스 스케쥴링 방식을 구현하기 위해 많이 사용됨 (운영체제 참조)

<br>

## 1. 파이썬 Queue 라이브러리 활용

- `queue` 라이브러리에는 다양한 큐 구조로, `Queue()`, `LifoQueue()`, `PriorityQueue()` 제공
- 프로그램을 작성할 때 프로그램에 따라 적합한 자료 구조를 사용

    일반적인 큐 외에 다양한 정책이 적용된 큐들이 있음

    - Queue : 가장 일반적인 큐 자료 구조
    - LifoQueue: 나중에 입력된 데이터가 먼저 출력되는 구조 (스택 구조라고 보면 됨)
    - PriorityQueue: 데이터마다 우선순위를 넣어서, 우선순위가 높은 순으로 데이터 출력

<br>

### 1-1. Queue() (FIFO: First-In, First-Out)

```python
import queue

q = queue.Queue()

q.put('data1')
q.put(2)

q.qsize() # 2

q.get() # data1
q.get() # 2

q.qsize() # 0
```

<br>

### 1-2. LifoQueue() (LIFO: Last-In, First-Out)

```python
import queue

q = queue.LifoQueue()

q.put('data1')
q.put(2)

q.qsize() # 2

q.get() # 2
q.get() # data1

q.qsize() # 0
```

<br>

### 1-3. PriorityQueue()

```python
import queue

q = queue.PriorityQueue()

q.put((10, 'data1'))
q.put((1, 'data2'))
q.put((5, 'data3'))

q.get() # (1, 'data2')
q.get() # (5, 'data3')
q.get() # (10, 'data1')
```

<br>

## 2. 문제 활용 : 프로그래머스 (스택/큐) - 탑

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