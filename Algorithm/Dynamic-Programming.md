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

<br>

## Q. 정수 삼각형

```python
def solution(triangle):
    totals = [0]
    for idx, line in enumerate(triangle):
        new_totals = [0 for i in range(idx+1)]
        for index1, number1 in enumerate(totals):
            for index2 in range(index1, len(line))[:2]:
                number2 = line[index2]
                if new_totals[index2] <= number1 + number2:
                    new_totals[index2] = number1 + number2
        totals = new_totals
    return max(totals)
```

<br>

## Q. 등굣길

> 아무리 생각해도 이게 정답 같은데.... (더 알아봐야할 것 같다.)

```python
def solution(m, n, puddles):
    roads = [[0 for i in range(m+1)] for i in range(n+1)]
    roads[0][1] = 1

    for y in range(1, n+1):
        for x in range(1, m+1):
            roads[y][x] = roads[y-1][x] + roads[y][x-1]
            if [y, x] in puddles:
                roads[y][x] = 0
    return roads[n][m] % 1000000007
```
