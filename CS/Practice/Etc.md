## Q. 124 나라

> 3진수를 이용하면 될 것 같은데, 죽어도 안되더라 한 시간 정도 해맨거 같다. 원래 자연수와 1 정도 차이나길래 가장 첫 부분에 1을 빼고 시작했는데도 답이 안나와서 해맸다.

```python
def solution(n):
    rules = ['1', '2', '4']
    answer = ''
    while n > 0:
        n -= 1
        n, mod = divmod(n, 3)
        answer = rules[mod] + answer
    return answer
```

<br>

## Q. 완수하지 않은 선수

> 다 지워가면서 마지막에 남은 선수만 리턴하는 방식으로 풀었음.

```python
from collections import defaultdict

def solution(participant, completion):
    participants = defaultdict(int)
    while participant:
        participants[participant.pop()] += 1
    while completion:
        participants[completion.pop()] -= 1

    return sorted(participants.items(), key=lambda x: x[1], reverse=True)[0][0]
```

<br>

## Q. 크레인 인형뽑기 게임

> 로직상 문제가 없어보이는데, 디버깅 해보니 break문을 안 건 것이 문제였다. 조건에 만족하면 반복문에서 나와야하는 경우 break를 잊지말자.

```python
def solution(board, moves):
    answer, stack = 0, [0]
    for move in moves:
        for line in board:
            if line[move-1] > 0:
                doll = line[move-1]
                line[move-1] = 0
                if stack[-1] == doll:
                    stack.pop()
                    answer += 2
                else:
                    stack.append(doll)
                break
    return answer
```

<br>

## Q. 소수 찾기

> 속도가 안 나옴, 전공 과목 시간에 배운대로 소수는 소수로 나눠지면 안되는걸 착안해서 코드 작성,

```python
def solution(n):
    answer, primes = 1, [2]
    if n < 3:
        return answer

    for number in range(3, n+1):
        for prime in primes[:200]:      # 이 부분이 가장 큰 키포인트
            isPrime = True
            if number % prime == 0:
                isPrime = False
                break
        if isPrime:
            primes.append(number)
            answer += 1
    return answer
```

<br>

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

<br>

## Q. 두개 뽑아서 더하기

> 2 이상 100 이하의 자연수가 담겨있는 배열에서 두 개의 수를 뽑아 더해 만들 수 있는 모든 수를 정렬하시오.

```python
def solution(numbers):
    answer = set()
    for i in range(len(numbers)-1):
        for j in range(i+1, len(numbers)):
            answer.add(numbers[i]+numbers[j])
    return sorted(answer)
```

아래는 `itertools.combinations` 를 이용하여 풀었다.

```python
from itertools import combinations

def solution(numbers):
    return sorted(set(sum(comb) for comb in combinations(numbers, 2)))
```

<br>

## Q. 삼각 달팽이

> 배열만 이용해서 풀어보려고 헤딩을 좀 많이 했다. 규칙을 발견하는데 많이 버벅여서 처음에는 코드가 엄청 길게 짜였다. 정답을 맞추고도 이래도 되나 싶었는데, 다른 풀이를 참고해서 깔끔하게 수정했다.

```python
def solution(n):
    answer = [[0 for j in range(i+1)] for i in range(n)]
    MAX_N = sum(len(ans) for ans in answer)
    N, i, j = 1, 0, 0
    answer[0][0] = 1
    while N < MAX_N:
        while True:
            if (i+1 >= n) or (answer[i+1][j] != 0): break;
            i+=1
            N+=1
            answer[i][j] = N

        while True:
            if (j+1 >= n) or (answer[i][j+1] != 0): break
            j+=1
            N+=1
            answer[i][j] = N

        while True:
            if (i-1 < 0) or (j-1 < 0) or (answer[i-1][j-1] != 0): break
            i-=1
            j-=1
            N+=1
            answer[i][j] = N
    result = []
    for ans in answer:
        result += ans

    return result
```

<br>

## Q. 90도 회전

> 문제는 아니지만, 문제 풀면서 기억했으면 했던 코드이다.

```python
def rotate_90_degree(array, clock=True):
    new_array = []
    if clock:
        for col in range(len(array[0])):
            row = list(map(lambda x: x[i], array))
            row.reverse()
            new_array.append(row)
    else:
        for col in range(len(array[0]), 0, -1):
            row = list(map(lambda x: x[i-1], array))
            new_array.append(row)

    return True
```

<br>

## Q. n진수

> 📌 기억

```python
# 10진수 -> n진수
def n_decimal(number, n=2):
    T = "0123456789ABCDEF"
    q, r = divmod(number, n)
    if q == 0:
        return T[r]
    else:
        return n_decimal(q, n) + T[r]
```

<br>

```python
# n진수 -> 10진수
answer = 0
for idx, i in enumerate(three_reverse[::-1]):
		answer += int(i) * ( n ** idx )
```

