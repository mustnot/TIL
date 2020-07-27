## Q. 타일 장식물
> 피보나치 수열과 동적 계획법을 이용하여 풀이

```python
def solution(N):
    tiles = [0, 1] + [0 for i in range(N)]
    for n in range(2, N+2):
        tiles[n] = sum(tiles[n-2:n])
    return sum(tiles[-2:]) * 2
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

