## Dynamic Programming Practice

### Programmers > 동적계획법(Dynamic Programming) > 타일 장식물

|정확성|효율성|합계|
|:----:|:----:|:----:|
|66.7|33.3|100.0/100.0|

```python
def solution(N):
    tiles = [1, 1]
    for ix in range(2, N+1):
        tiles.append(tiles[ix-1] + tiles[ix-2])
    return (tiles[N] + tiles[N-1]) * 2
```