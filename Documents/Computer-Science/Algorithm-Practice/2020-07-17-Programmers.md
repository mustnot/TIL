# Level 2

## 문제 1

OO 연구소는 한 번에 K 칸을 앞으로 점프하거나, (현재까지 온 거리) x 2 에 해당하는 위치로 순간이동을 할 수 있는 특수한 기능을 가진 아이언 슈트를 개발하여 판매하고 있습니다. 이 아이언 슈트는 건전지로 작동되는데, 순간이동을 하면 건전지 사용량이 줄지 않지만, 앞으로 K 칸을 점프하면 K 만큼의 건전지 사용량이 듭니다. 그러므로 아이언 슈트를 착용하고 이동할 때는 순간 이동을 하는 것이 더 효율적입니다. 아이언 슈트 구매자는 아이언 슈트를 착용하고 거리가 N 만큼 떨어져 있는 장소로 가려고 합니다. 단, 건전지 사용량을 줄이기 위해 점프로 이동하는 것은 최소로 하려고 합니다. 아이언 슈트 구매자가 이동하려는 거리 N이 주어졌을 때, 사용해야 하는 건전지 사용량의 최솟값을 return하는 solution 함수를 만들어 주세요.

```python
def solution(n):
    ans = 0
    
    while n > 0:
        jump = n % 2
        n -= jump
        n = n // 2
        ans += jump

    return ans
```

<br>

## 문제 2

프로그래머스 팀에서는 기능 개선 작업을 수행 중입니다. 각 기능은 진도가 100%일 때 서비스에 반영할 수 있습니다.

또, 각 기능의 개발속도는 모두 다르기 때문에 뒤에 있는 기능이 앞에 있는 기능보다 먼저 개발될 수 있고, 이때 뒤에 있는 기능은 앞에 있는 기능이 배포될 때 함께 배포됩니다.

먼저 배포되어야 하는 순서대로 작업의 진도가 적힌 정수 배열 progresses와 각 작업의 개발 속도가 적힌 정수 배열 speeds가 주어질 때 각 배포마다 몇 개의 기능이 배포되는지를 return 하도록 solution 함수를 완성하세요.

```python
import math

def solution(progresses, speeds):
    answer = []

    remain_times = []
    for progress, speed in zip(progresses, speeds):
        remain_times.append(math.ceil((100 - progress) / speed))

    deploy, prev_date = 1, remain_times[0]
    for date in remain_times[1:]:
        if date <= prev_date:
            deploy += 1
        else:
            answer.append(deploy)
            deploy = 1
            prev_date = date
    answer.append(deploy)

    return answer
```

<br>

## 문제 3
> 효율성 해결 필요

초 단위로 기록된 주식가격이 담긴 배열 prices가 매개변수로 주어질 때, 가격이 떨어지지 않은 기간은 몇 초인지를 return 하도록 solution 함수를 완성하세요.


```python
def solution(prices):
    answer = []
    for ix, price in enumerate(prices):
        seconds = 0
        for next_price in prices[ix:]:
            seconds += 1
            if next_price < price:
                break
        answer.append(seconds-1)
                
    return answer
```

<br>

# Level 3

## 문제 1
> 다시 풀어야함

가로 길이가 2이고 세로의 길이가 1인 직사각형모양의 타일이 있습니다. 이 직사각형 타일을 이용하여 세로의 길이가 2이고 가로의 길이가 n인 바닥을 가득 채우려고 합니다. 타일을 채울 때는 다음과 같이 2가지 방법이 있습니다.

```python
def solution(n):
    combinations = [[0, '']]
    
    answer, answers = 0, []
    for i in range(n):
        new_combinations = []
        for block_length, block in [[1, '1'], [2, '2']]:
            for length, combination in combinations:
                new_length = length + block_length
                new_combination = combination + block
                if new_length == n:
                    if new_combination not in answers:
                        answers.append(new_combination)
                        answer += 1
                if new_length < n:
                    new_combinations.append([new_length, new_combination])
        combinations = new_combinations
    return answer % 1000000007
```


