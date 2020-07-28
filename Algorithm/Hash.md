## Q. 해시, 집합, 그래프 - 친구 네트워크
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

<br>

## Q. 위장
> ✔ 코드를 작성하기 보다 오히려 조합이라는 키워드에 맞춰 조합식을 코드로 작성함
```python
from collections import defaultdict

def solution(clothes):
    clothes_dict = defaultdict(int)
    for cloth, category in clothes:
        clothes_dict[category] += 1
    
    answer = 1
    for n in clothes_dict.values():
        answer = answer * (n+1)
    return answer - 1
```

<br>

## Q. 베스트 앨범

```python
import queue
from collections import defaultdict

def solution(genres, plays):
    answer = []

    genres_dict = defaultdict(dict)
    genres_play = defaultdict(int)
    for ix, (genre, play) in enumerate(zip(genres, plays)):
        genres_play[genre] += play
        genres_dict[genre][ix] = play
    
    
    for genre, sum_play in sorted(genres_play.items(), key=lambda x: x[1], reverse=True):
        for ix, play in sorted(genres_dict[genre].items(), key=lambda x: x[1], reverse=True)[:2]:
            answer.append(ix)
        
    return answer
```