## Q. 라면 공장
> 다시 풀어야함
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

<br>

## Q. 더 맵게
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