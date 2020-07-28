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

