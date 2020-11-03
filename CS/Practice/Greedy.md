## 큐, 구현, 그리디 - 프린터 큐
> ✔ 테스트 케이스 8개 정도 찾아서 맞췄지만, 20개 중 8개가 틀림, 경우의 수를 한번 더 생각해봐야겠음. (나중에 다시 보기)
```python
import queue

def solution(priorities, location):
    q = queue.PriorityQueue()
    if len(priorities) == 1:
        return 1
    
    for ix, priority in enumerate(priorities):
        q.put((-priority, ix, ix))
    
    answers, last_location = [], -1
    while q.qsize():
        ix += 1
        priority, loc, index = q.get()
        if last_location <= loc:
            last_location = loc
            answers.append(index)
        else:
            q.put((priority, ix, index))            
    
    return answers.index(location) + 1

```

<br>

## Q. 스택, 구현, 그리디 - 키로거
> ✔ pop() 전에 스택 안에 데이터가 존재하는지 확인 꼭 필요 !

```python
def solution(commands):
    result = []

    left = []
    for command in commands:
        if command == '<':
            if result:
                left.append(result.pop())
        elif command == '>':
            if left:
                result.append(left.pop())
        elif command == '-':
            if result:
                result.pop()
        else:
            result.append(command)
    left.reverse()
    print(''.join(result + left))
```

<br>

## Q. 점프와 순간이동
```python
def solution(n):
    ans = 0
    
    while n > 0:
        jump = n % 2
        n -= jump
        n = n // 2
        ans += jump

    return ans
```

<br>

## Q. 체육복

> ℹ️ 제한 사항을 잘 읽자!

```python
def solution(n, lost, reserve):
    clothes = {i: 1 for i in range(1, n+1)}
    for i in range(1, n+1):
        if i in reserve: clothes[i] += 1
        if i in lost: clothes[i] -= 1

    for i, cloth in clothes.items():
        if cloth == 0:
            if clothes.get(i-1) == 2:
                clothes[i-1] -= 1
                clothes[i] += 1
            elif clothes.get(i+1) == 2:
                clothes[i+1] -= 1
                clothes[i] += 1    
    return len(list(filter(lambda x: x>0, clothes.values())))
```

### 틀렸던 코드들

> 제한 사항을 제대로 읽었어야했는데, 제한 사항 중 여벌 체육복을 가져온 학생이 도난 당했을 가능성을 배제하고 코드를 짜서 틀렸다.

```python
def solution(n, lost, reserve):
    clothes = {}    
    for i in range(1, n+1):
        if i in lost:
            if i+1 in reserve:
                clothes[i] = 1
                clothes[i+1] = 1
                lost.remove(i)
                reserve.remove(i+1)
        elif i in reserve:
            if i+1 in lost:
                clothes[i] = 1
                clothes[i+1] = 1
                lost.remove(i+1)
                reserve.remove(i)
            else:
                clothes[i] = 2
        else:
            clothes[i] = 1
    return len(clothes)
```

```python
def solution(n, lost, reserve):
    clothes = []
    for i in range(1, n+1):
        if i in lost: clothes.append(0)
        elif i in reserve: clothes.append(2)
        else: clothes.append(1)

    for i, cloth in enumerate(clothes):
        if cloth == 0:
            if i-1 > -1 and clothes[i-1] == 2:
                clothes[i-1] = 1
                clothes[i] = 1
            elif i+1 < n and clothes[i+1] == 2:
                clothes[i+1] = 1
                clothes[i] = 1
```

<br>

## Q. 큰 수 만들기 (시간 초과)

> 실행 속도 개선이 필요하다.

틀린 코드 1 : 시간 초과로 런타임이 긴 문제가 있음

```python
def solution(number, k):
    answer = []
    while k > 0:
        answer = '0'
        k -= 1
        for i in range(len(number)-k):
            new_number = number[:i] + number[i+1:]
            if new_number > answer:
                answer = new_number
        number = answer
    return answer
```

