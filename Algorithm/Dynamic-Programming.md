## Q. 타일 장식물
> 대체로 동적 계획법 문제는 코드가 짧아지는 것 같다.

```python
def solution(N):
    tiles = [1, 1]
    for ix in range(2, N+1):
        tiles.append(tiles[ix-1] + tiles[ix-2])
    return (tiles[N] + tiles[N-1]) * 2
```
```python
def solution(N):
    tiles = [0, 1] + [0 for i in range(N)]
    for n in range(2, N+2):
        tiles[n] = sum(tiles[n-2:n])
    return sum(tiles[-2:]) * 2
```

