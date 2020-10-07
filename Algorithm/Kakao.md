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

<br>

## Q. 후보키 (2019)

> 최소성 규칙을 위해서 앞서서 key를 제거하며 후보키를 만들었는데, 이것 때문에 정확성이 떨어졌다.

```python
from itertools import combinations

def solution(relations):
    answer = []
    keys = [key for key in range(len(relations[0]))]
    
    key_length = 1
    while key_length <= len(relations[0]):
        key_combs = list(combinations(keys, key_length))
        
        while key_combs:
            comb = key_combs.pop(0)
            isUnique, tuples_list = True, []
            for relation in relations:
                tuples = [relation[idx] for idx in comb]
                if tuples not in tuples_list:
                    tuples_list.append(tuples)
                else:
                    isUnique = False
                    break
            if isUnique:
                isMin = True
                for ans in answer:
                    if set(ans).issubset(set(comb)):
                        isMin = False
                        break
                if isMin:
                    answer.append(comb)
        key_length += 1

    return len(answer)
```

정확성 : 82.1 점

```python
from itertools import combinations

def solution(relations):
    answer = []
    keys = [key for key in range(len(relations[0]))]
    
    key_length = 1
    while key_length <= len(relations[0]):
        key_combs = list(combinations(keys, key_length))
        
        while key_combs:
            comb = key_combs.pop(0)
            isUnique, tuples_list = True, []
            for relation in relations:
                tuples = [relation[idx] for idx in comb]
                if tuples not in tuples_list:
                    tuples_list.append(tuples)
                else:
                    isUnique = False
                    break
            if isUnique:
                answer.append(comb)
                for key in comb:
                    if key in keys:
                        keys.remove(key)
        key_length += 1
    return len(answer)
```

<br>

## Q. 무지의 먹방 라이브 (2019)

> 마지막 부분에 대한 이해를 다시 한번 할 필요가 있음.

```python
from queue import PriorityQueue

def solution(food_times, k):
    if sum(food_times) <= k:
        return -1
    
    queue = PriorityQueue()
    for i, food_time in enumerate(food_times):
        queue.put((food_time, i+1))
    
    total_time, prev_time = 0, 0
    while total_time + ((queue.queue[0][0] - prev_time) * (queue.qsize())) <= k:
        food_time, idx = queue.get()
        total_time += (food_time - prev_time) * (queue.qsize() + 1)
        prev_time = food_time

    target = k - total_time + 1
    temp = (target - 1) // queue.qsize()
    result = sorted(queue.queue, key=lambda x: x[1])[0]
    target -= temp * queue.qsize()

    return result[target-1][1]
```

* 효율성 실패

```python
def solution(food_times, k):
    if sum(food_times) <= k:
        return -1
    
    food_times = [(i+1, food) for i, food in enumerate(food_times)]
    while k > 0:
        k -= 1
        if food_times:
            ix, food = food_times.pop(0)
            if food > 1:
                food_times.append((ix, food-1))
        else:
            return -1
    
    if food_times:
        return food_times.pop(0)[0]
    else:
        return -1
```

<br>

## Q. 매칭 점수 (2019)

> 처음 해당 페이지의 URL을 가져오는 regex가 잘못되어 실패하는 경우가 있었다.

```python
import re

url_regex = re.compile('\<meta property\=\"og\:url\" content\=\"https\:\/\/(\S*)"', re.I)

def solution(word, pages):
    page_scores = {url_regex.search(page).group(1): [ix, 0, 0, 0] for ix, page in enumerate(pages)}

    for page in pages:
        url = url_regex.search(page).group(1)

        base_score = re.findall("([a-z]+)", page.lower(), flags=re.IGNORECASE).count(word.lower())
        page_scores[url][1] = base_score
        external_links = re.findall(f'<a href="https\:\/\/(\S*)"', page, re.I)
        print(external_links)
        page_scores[url][2] = len(external_links)

        for link in external_links:
            if link in page_scores:
                page_scores[link][3] += base_score / len(external_links)
    
    return sorted(page_scores.items(), key=lambda v: v[1][1] + v[1][3], reverse=True)[0][1][0]
```

<br>

## Q. 뉴스 클러스터링 (2018)

```python
import re

def getElement(word):
    elements = []
    for ix in range(len(word)-1):
        e = word[ix] + word[ix+1]
        if e.isalpha(): 
            while e in elements:
                e += "+"
            elements.append(e)
    return set(elements)

def solution(str1, str2):
    str1, str2 = getElement(str1.lower()), getElement(str2.lower())
    
    try:
        similarity = len(str1 & str2) / len(str1 | str2)
        print(similarity)
    except ZeroDivisionError:
        similarity = 1
    
    return int(similarity * 65536)
```

<br>

## Q. 캐시 (2018)

> Class 까지 만들 필요 없이 바로 짤 수 있었지만, 한 번 짜보고 싶었다.

```python
from collections import deque

class LRU:
    def __init__(self, cacheSize=1):
        self.cacheList = deque(maxlen=cacheSize)
        self.runtime = 0
    
    def get(self, value):
        value = value.lower()
        if value in self.cacheList:
            self.cacheList.remove(value)
            self.cacheList.appendleft(value)
            self.runtime += 1
        else:
            self.cacheList.appendleft(value)
            self.runtime += 5

        return value

def solution(cacheSize, cities):
    lru = LRU(cacheSize=cacheSize)

    for city in cities:
        lru.get(city)
    
    return lru.runtime
```

<br>

## Q. 파일명 정렬 (2018)

> Number 구하는데 너무 오랜 시간 걸렸다. 정규식을 너무 잘 짜려고 해도 시간 낭비일 수 있다.

```python
import re

def solution(files):
    split_files = []
    for ix, file in enumerate(files):
        head = re.search("(^[a-zA-Z\.\-\s]+)", file).group(0)
        number = re.search("([0-9]+)", file).group(0)[:5]
        split_files.append([ix] + [head, number] + [file])

    return [v[-1] for v in sorted(split_files, key=lambda x: (x[1].lower(), int(x[2]), x[0]))]
```

<br>

## Q. 압축 (2018)

> 단순 구현 문제라 문제만 잘 읽는다면 푸는데 어려움은 없을 것 같다.

```python
import string

def solution(msg):
    answer = []

    msg = [m for m in msg]
    dictionary = [s for s in string.ascii_uppercase]

    while msg:
        wc = msg.pop(0)
        while msg and wc in dictionary:
            wc += msg.pop(0)
        if wc in dictionary:
            answer.append(dictionary.index(wc))
        else:
            w, c = wc[:-1], wc[-1]
            msg = [_ for _ in c] + msg
            answer.append(dictionary.index(w))
            dictionary.append(w+c)
        
    return [_+1 for _ in answer]
```

