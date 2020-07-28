## Q. 단어 변환

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
