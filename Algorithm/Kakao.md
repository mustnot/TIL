# KAKAO BLIND RECRUITMENT

> 😭 실제 코딩 테스트 문제를 풀어보는 것이 좋을 것 같다..

<br>

## Q. 문자열 압축 (2020)

> 📌 정규 표현식(regular expression)을 적극 사용하자 !

```python
import re

def solution(s):
    answer = 1000
    for N in range(1, len(s)//2 + 2):
        splitList = re.sub('(\w{%s})' % N, '\g<1> ', s).split()
        i, res_word = 0, ''
        while i < len(splitList):
            cnt = 1
            for j in range(i+1, len(splitList)):
                if splitList[i] == splitList[j]:
                    cnt += 1
                else:
                    break
            if cnt >= 2:
                res_word += str(cnt) + splitList[i]
            else:
                res_word += splitList[i]
            i += cnt
        if res_word and len(res_word) < answer:
            answer = len(res_word)
    return answer
```

<br>

## Q. 가사 검색 (효율성 테스트 실패) (2020)

> 단어와 검색 키워드가 길수록 dict에서 key를 찾는데 오래걸리는 문제가 있어 다른 접근 방법으로 가야할 것 같다.

```python
import re

def solution(words, queries):
    cache, answer = {}, []
    for query in queries:
        if query in cache:
            answer.append(cache[query])
        else:
            regex = "^" + re.sub("\?", ".", query) + "$"
            r = re.compile(regex)
            answer.append(len(list(filter(r.match, words))))
    return answer
```

<br>

## Q. 오픈 채팅방 (2019)

```python
def solution(records):
    user = {}
    for i, record in enumerate(records):
        command, uid, name = (record.split() + [""])[:3]
        if name:
            user[uid] = name

    answer = []
    for record in records:
        command, uid, name = (record.split() + [""])[:3]
        if command == "Enter":
            answer.append("%s님이 들어왔습니다." % user[uid])
        elif command == "Leave":
            answer.append("%s님이 나갔습니다." % user[uid])

    return answer
```

<br>

## Q. 실패율 (2019)

> 📌 개수를 셀 때에는 filter 대신에 count도 있다.

```python
def solution(N, stages):
    prob_dict = {}
    
    for stage in range(1, N+1):
        stages = list(filter(lambda x: x >= stage, stages))
        fail_players = stages.count(stage)
        try:
            prob_dict[stage] = fail_players / len(stages)
        except ZeroDivisionError:
            prob_dict[stage] = 0
            break
    
    return sorted([stage for stage in range(1, N+1)], key=lambda x: prob_dict.get(x, 0), reverse=True)
```

아래 코드는 테스트 22에서 시간 초과로 틀린 코드이다. 

```python
def solution(N, stages):
    prob_dict = {}
    
    for stage in range(1, N+1):
        stages = list(filter(lambda x: x >= stage, stages))
        players = len(stages)
        fail_players = len(list(filter(lambda x: x == stage, stages)))
        try:
            prob_dict[stage] = fail_players/players
        except ZeroDivisionError:
            prob_dict[stage] = 0

    return sorted(prob_dict.keys(), key=lambda x: prob_dict.get(x, 0), reverse=True)
```

