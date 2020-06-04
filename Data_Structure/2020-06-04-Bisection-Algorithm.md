## Bisection-Algorithm

### 연습 1. Programmers > 이분 탐색 > 예산
> ✔ feedback : 두 가지 경우를 감안하지 못하고 작성한게 시간이 걸린 원인이였다. 첫번 째는 최소값을 예산의 최소값으로 잡았는데, 그 아래의 값이 최대 예산일 수 있다는 점, 두번 재는 예산의 합이 총 예산의 합보다 작을 수 있다는 점 무작정 짜지말고 한번 쯤 고민해볼 것 !

|정확성|효율성|합계|
|:----:|:----:|:----:|
|75.0|25.0|100/100.0|

```python
def solution(budgets, M):
    _min, _max = 0, max(budgets)
    if sum(budgets) <= M:
        return _max
    
    recent_middle = 0
    while True:
        middle = int((_min + _max) // 2)
        if middle == recent_middle:
            break
        _sum = sum([budget if budget <= middle else middle for budget in budgets])
        if _sum > M:
            _max = middle
        elif _sum == M:
            return middle
        else:
            _min = middle
        recent_middle = middle

    return middle

```