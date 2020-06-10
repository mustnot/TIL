## Advance Data Structure Algorithm Practice


### 해시, 집합, 그래프 - 친구 네트워크 - 난이도 중
> ✔ 시간 초과가 났는데, 아무래도 네트워크가 많아지면 많아질수록 탐색하는 키가 많아져서 느려진 것 같다. 우선 테스트는 성공
```python
from collections import defaultdict

case = int(input())

for _ in range(case):
    F = int(input())
    
    network = defaultdict(set)
    for relation in range(F):
        _from, _to = input().split()
        network[_from].update([_to])
        network[_to].update([_from])
        
        visited, not_visited = [], set([_from, _to])
        while not_visited:
            next_visit = not_visited.pop()
            if next_visit not in visited:
                visited.append(next_visit)
                not_visited.update(network[next_visit])
        print(len(visited))
```


## Sort Algorithm Practice

### 정렬 - 소트 인사이드 - 난이도 하
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


### 정렬 - 나이순 정렬 - 난이도 하
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