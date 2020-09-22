# 2020 KAKAO BLIND RECRUITMENT

> 😭 실제 코딩 테스트 문제를 풀어보는 것이 좋을 것 같다..

<br>

## Q. 문자열 압축

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

## Q. 가사 검색 (효율성 테스트 실패)

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

