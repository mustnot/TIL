## Q. 정렬 - 소트 인사이드
> ✔ python 내장 함수 중 하나인 max를 쓰는 방법과 일반적인 방법을 나눠서 연습
```python
number = [int(n) for n in input()]
while number:
    print(max(number), end='')
    number.remove(max(number))
```
```python
number = [int(n) for n in input()]
for n in range(9, -1, -1):
    while n in number:
        number.remove(n)
        print(n, end='')
```

<br>

## Q. 정렬 - 나이순 정렬
> ✔ python 내장함수인 sorted를 사용하는 방법도 있지만, python 데이터 구조인 set의 가장 작은 수 먼저 pop 되는 성질을 이용하여 문제 풀이
```python
count = int(input())

ages, members = set(), []
for _ in range(count):
    member_info = input().split()
    age, name = int(member_info[0]), member_info[1]
    ages.update([age])
    members.append((age, name))

while ages:
    min_age = ages.pop()
    for age, member in members:
        if min_age == age:
            print(age, member)
```

<br>

## Q. 좌표 구현하기
> PriorityQueue를 활용해서 품, 코드로는 문제가 없어보이는데 런타임 에러가 발생 - 원인 파악이 안됨

```python
import queue

point_cnt = int(input())

points = queue.PriorityQueue()
for _ in range(point_cnt):
    x, y = list(map(int, input().split()))
    points.put((x, y))

while points.qsize():
    coordinates = points.get()
    print(coordinates[0], coordinates[1])
```

<br>

## Q. 수 정렬하기 3
> input()을 쓸 때랑 sys.stdin.readline() 할 때의 차이가 존재함. input()일 때는 시간초과로 못 읽는 경우가 있음.. (단순히 채점 서버 문제인가..)
```python
import sys
from collections import defaultdict

number_cnt = int(sys.stdin.readline())

numbers = defaultdict(int)
for _ in range(number_cnt):
    n = int(sys.stdin.readline())
    numbers[n] += 1

for n in range(10001):
    if numbers[n] > 0:
        for j in range(numbers[n]):
            print(n)
```

<br>

## Q. 가장 큰 수
> 정렬이라는 키워드에서 sorted를 생각하고, 수 정렬로는 안되고 문자열 정렬로 보니 해결 가능할 것으로 보였음.. 하지만 가락만 잡고 정답은 아님
```python
def solution(numbers):
    numbers = [str(number) for number in numbers]
    return ''.join((sorted(numbers, reverse=True)))
```