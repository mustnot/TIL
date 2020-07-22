## 문제 : 124 나라
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

## 문제 : 크레인 인형뽑기 게임
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

## 문제
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