## Q. 가장 먼 노드

> 가장 먼 노드라고 해서 DFS 를 생각하고 접근했는데, BFS로 순차적으로 돌면서 거리를 계산하는게 주요했던 것 같다.

```python
from collections import deque

def bfs(graph, start, distances):
    dist, q = 0, deque([[start, dist]])
    while q:
        value = q.popleft()
        start, dist = value[0], value[1]
        if distances[start] == -1:
            distances[start] = dist
            dist += 1
            for e in graph[start]:
                q.append([e, dist])

def solution(n, edge):
    answer = 0
    distances = [-1 for i in range(n+1)]
    graph = {i:[] for i in range(n+1)}
    for a, b in edge:
        graph[a].append(b)
        graph[b].append(a)
    bfs(graph, 1, distances)

    print(distances)
    return len(list(filter(
        lambda x: x == max(distances),
        distances
        )))
    
print(solution(6, [[3, 6], [4, 3], [3, 2], [1, 3], [1, 2], [2, 4], [5, 2]]))
```

<br>

## Q. 순위

> 가장 중요한 키포인트는 말이 좀 어려운데, 
>
> 1. N이 이긴 선수들은 N이 졌던 선수들, 즉 N을 이겼던 선수들한테도 진다라는 명제
> 2. N이 졌던 선수들은 N을 이겼던 선수들, 즉 N한테 졌던 선수들도 이긴다라는 명제
>
> 두 가지 명제가 가장 중요했던 것 같다.

```python
def solution(n, results):
    wins, loses = {i:set() for i in range(1, n+1)}, {i:set() for i in range(1, n+1)}
     
    for winner, loser in results:
        wins[winner].update([loser])
        loses[loser].update([winner])
    
    for i in range(1, n+1):
        for j in wins[i]:
            loses[j].update(loses[i])
        for j in loses[i]:
            wins[j].update(wins[i])
    
    answer = 0
    for i in range(1, n+1):
        if len(loses[i]) + len(wins[i]) == n-1:
            answer+=1
    return answer
```

