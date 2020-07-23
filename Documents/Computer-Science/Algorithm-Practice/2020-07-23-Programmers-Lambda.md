# Python Lambda
> Programmers 푼 문제 중 lambda를 사용하며 푼 문제 중 일부만 정리했고, 다시 눈에 보기 쉽게 `Level 1` 의 문제만 정리했다.

## Q. 모의고사
`lambda`와 `map`을 이용해서 푼 문제인데, 여기서는 `map`을 이용해 정답인 경우에 `True`가 리턴되는 함수를 만들어 `True`를 합산하면 점수가 나오게 되는 방식을 이용함

```python
from collections import defaultdict

def solution(answers):
    answer_len = len(answers)

    first = ([1, 2, 3, 4, 5] * 2000)[:answer_len]
    second = ([2, 1, 2, 3, 2, 4, 2, 5] * 1250)[:answer_len]
    third = ([3, 3, 1, 1, 2, 2, 4, 4, 5, 5] * 1000)[:answer_len]
    
    scores = defaultdict(list)
    for ix, answer in enumerate([first, second, third]):
        scores[sum(map(lambda x: x[0]==x[1], zip(answer, answers)))].append(ix+1)
    
    return sorted(scores.items(), key=lambda x:x[0], reverse=True)[0][1]
```

<br>

## Q. 나누어 떨어지는 숫자 배열
`filter`와 `lambda`를 이용해서 풀었는데, `python`의 내장 함수인 `filter`는 조건에 만족하는 값을 리턴하는 내장 함수이다. 여기서는 나누어 떨어지는 조건에 만족하는 값만 리턴하면 되기에 `lambda`를 이용해 조건을 만들고 `filter`로 걸러내는 함수를 생성했다.

```python
def solution(arr, divisor):
    answer = list(filter(lambda x: x%divisor==0, sorted(arr)))
    if answer:
        return answer
    return [-1]
```

<br>

## Q. 문자열 내 마음대로 정렬하기
정말 간단한 문제였는데, 문자의 인덱스와 해당 문자의 순서를 기준으로 정렬하는 문제였다. `lambda`를 이용해 두 개의 정렬 기준을 리턴하게 하여 정렬되도록 함

```python
def solution(strings, n):
    return sorted(strings, key=lambda word: (word[n], word))
```

<br>

## Q. 이상한 문자 만들기
중간에 조건문으로 해도 됐을 것 같지만 `lambda`로 함수 만들고 싶었다. 

```python
def solution(s):
    f = lambda ix, w: w.upper() if ix%2 == 0 else w.lower()
    ix, answer = 0, ''
    for string in s:
        if string == ' ':
            answer += string
            ix = 0
        else:
            answer += f(ix, string)
            ix += 1

    return answer
```