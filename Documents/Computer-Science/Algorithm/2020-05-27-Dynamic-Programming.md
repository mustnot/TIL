# 동적 계획법 (Dynamic Programming)

> ✔ 코드를 만들다보면, 함수 안에 너무 많은 값을 저장하는 것을 기피했는데, 동일한 과정이 반복된다면, 저장하여 속도를 높이는 것도 좋으니 적극 활용해보자 !

- **동적계획법 :** 입력 크기가 작은 부분 문제들을 해결한 후 해를 활용해서 보다 큰 크기의 부분 문제를 해결, 최종적으로 전체 문제를 해결하는 알고리즘
- **상향식 접근법**으로 **최하위**에서 시작하여, 최하위 해답을 구한 후 이를 토대로 상위 문제를 풀어가는 방식을 의미
- **Memoization 기법**을 사용하는데, Memoization이란, 프로그램 실행 시 이전에 계산한 값을 저장하여, 다시 계산하지 않도록 하여 실행 속도를 빠르게 하는 기술, 결국 과거 계산한 값을 기록해 놓는 것으로 생각하면 된다.
- 대표적인 예씨로, 피보나치 수열이 있다.

<br>

# 분할 정복 (Divide and Conquer)

- **분할 정복 :** 문제를 나눌 수 없을 때까지 나누어서 각각을 풀면서 다시 합병하여 문제의 답을 얻는 알고리즘
- 동적 계획법과 달리 **하향식 접근법**으로 상위의 해답을 구하기 위해 아래로 내려가면서 하위의 해답을 구하는 방식
- 일반적으로 재귀함수로 구현하며, 문제를 잘게 쪼개며, 부분 문제는 서로 중복되지 않는데, 병합 정렬, 퀵 정렬과 유사하다.

<br>

## 공통점과 차이점

- 공통점
    - 문제를 잘게 쪼개서, 가장 작은 단위로 분할
- 차이점
    - 동적 계획법
        - 부분 문제는 **중복**되어 **재활용**되어 상위 문제 해결시 사용된다.
        - **Memoization 기법**을 사용하는데, 부분 문제가 중복되어 재활용되기에 부분 문제를 저장해서 재활용하여 **최적화**에 이용한다.
    - 분할 정복
        - 부분 문제는 서로 **중복되지 않음**
        - **Memoization 기법** 사용하지 않음

<br>

## 동적 계획법 알고리즘 이해

- **피보나치 수열** : n을 입력받아 아래 공식에 의해 계산이 되는데, 여기서 F_(n_1) 과 F_(n-2) 을 **Memoization 기법**을 사용하여 저장하여 재활용한다면 **최적화**가 가능하다.

<p align="center">
  <img width="460" height="180" src="https://user-images.githubusercontent.com/52126612/83019044-4057f700-a061-11ea-8d31-14dfad6c22aa.png">
</p>


```python
# fibonachi (recursive call)
def fibonachi(n):
    if n <= 1:
        return n
    return fibonachi(n-1) + fibonachi(n-2)
```
```python
# fibonachi memoization 
# my code
def fibonachi_memo(n):
    fibonachi_db = [1 if ix == 1 else 0 for ix in range(n)]
    
    for ix in range(2, n+1):
        fibonachi_db[ix] = fibonachi_db[ix-1] + fibonachi_db[ix-2]
    return fibonachi_db[n]
```
```python
# study code
def fibo_dp(num):
    cache = [ 0 for index in range(num + 1)]
    cache[0] = 0
    cache[1] = 1
    
    for index in range(2, num + 1):
        cache[index] = cache[index - 1] + cache[index - 2]
    return cache[num]
```