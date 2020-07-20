## 문제
> 전에 배웠던 걸 응용해서 풀었는데, 풀렸다는 사실이 너무나 기분이 좋았다. 변수명을 똑같이 쓴 건 안비밀.. ㅎㅎ

두 개의 단어 begin, target과 단어의 집합 words가 있습니다. 아래와 같은 규칙을 이용하여 begin에서 target으로 변환하는 가장 짧은 변환 과정을 찾으려고 합니다.

```python
1. 한 번에 한 개의 알파벳만 바꿀 수 있습니다.
2. words에 있는 단어로만 변환할 수 있습니다.
```

예를 들어 begin이 hit, target가 cog, words가 [hot,dot,dog,lot,log,cog]라면 hit -> hot -> dot -> dog -> cog와 같이 4단계를 거쳐 변환할 수 있습니다.

두 개의 단어 begin, target과 단어의 집합 words가 매개변수로 주어질 때, 최소 몇 단계의 과정을 거쳐 begin을 target으로 변환할 수 있는지 return 하도록 solution 함수를 작성해주세요.

```python
from collections import defaultdict

def diff_words(word1, word2):
    return sum([1 if w1 != w2 else 0 for w1, w2 in zip(word1, word2)])

def solution(begin, target, words):
    answer = 0
    if target not in words:
        return 0
    
    visited, need_visited = [], [begin]

    while need_visited:
        answer += 1
        begin = need_visited.pop()
        visited.append(begin)
        for word in words:
            if diff_words(begin, word) == 1:
                need_visited.append(word)
                if word == target:
                    return answer
            
    return answer
```

<br>

## 문제

스트리밍 사이트에서 장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범을 출시하려 합니다. 노래는 고유 번호로 구분하며, 노래를 수록하는 기준은 다음과 같습니다.

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