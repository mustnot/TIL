## Advance Data Structure Algorithm Practice


### Level 1 - 위장
> ✔ 코드를 작성하기 보다 오히려 조합이라는 키워드에 맞춰 조합식을 코드로 작성함
```python
from collections import defaultdict

def solution(clothes):
    clothes_dict = defaultdict(int)
    for cloth, category in clothes:
        clothes_dict[category] += 1
    
    answer = 1
    for n in clothes_dict.values():
        answer = answer * (n+1)
    return answer - 1
```

<br>

### Level 1 - 탑
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

### Level 2 - 기능 개발
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

<br>

### Level 2 - 더 맵게
> ✔ 처음에 queue를 이용해서 풀어보려 했는데, heapq라는 더 쉬운 방법이 있어서 방법을 달리하여 재작성함, (조금 더 짧게 할 수 있었을텐데 아쉬움..)
```python
import heapq

def solution(scoville, K):
    if K == 0:
        return 0
    
    heapq.heapify(scoville)
    answer = 0
    while True:
        min1 = heapq.heappop(scoville)
        if scoville:
            min2 = heapq.heappop(scoville)
        else:
            if min1 < K:
                return -1
        if min1 < K:
            heapq.heappush(scoville, min1 + (min2 * 2))
            answer += 1
        else:
            break
    
    return answer
```

