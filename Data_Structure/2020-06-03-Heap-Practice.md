## Python Heapq Practice

```python
import heapq

heap = []
```

```python
# push : add value
heapq.heappush(heap, 8)
heapq.heappush(heap, 2)
heapq.heappush(heap, 4)
heapq.heappush(heap, 1)

print(heap)
# [1, 2, 4, 8]
```

```python
# pop : remove minimum value
print(heapq.heappop(heap))
# 1

print(heap)
# [2, 4, 8]
```

```python
# heapify : convert list to heap
heap = [8, 2, 4, 1]
heapq.heapify(heap)

print(heap)
# [1, 2, 4, 8]
```

<br>

### 연습 1. Programmers > 힙(Heap) > 더 맵게
|정확성|효율성|합계|
|:----:|:----:|:----:|
|76.2|23.8|100/100.0|

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

<br>

### 연습 2. Programmers > 힙(Heap) > 라면 공장
|정확성|효율성|합계|
|:----:|:----:|:----:|
|46.7|26.7|73.3/100.0|


```python
import heapq

def solution(stock, dates, supplies, k):
    supplies = [-supply for supply in supplies]
    heapq.heapify(supplies)
    dates.append(k)
    
    answer, prev_date = 0, 0
    for ix in range(len(dates)-1):
        stock -= dates[ix] - prev_date
        prev_date = dates[ix]
        need_supply = dates[ix+1] - dates[ix]
        if need_supply-1 < stock:
            continue

        while supplies and (stock < need_supply):
            supply = heapq.heappop(supplies)
            stock -= supply
            answer += 1

    return answer
```