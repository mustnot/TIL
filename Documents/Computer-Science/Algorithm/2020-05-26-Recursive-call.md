# 재귀 용법 (Recursive call, 재귀 호출)

> ✨ 알아도 제대로 사용하기 어렵던 재귀 함수... 연습하자 !

- 함수 안에서 동일한 함수를 호출하는 형태로 익숙해져야 함
- 대표적인 예시로 팩토리얼(n!) 이 있다. (작성해보자 !)

```python
def factorial(n):
	if n <= 1:
		return n
	else:
		return n * factorial(n-1)
```

### 시간 복잡도와 공간 복잡도

- `factorial(n)`은 **n-1**번의 `factorial()` 함수를 호출하며 곱셈을 하는데, 결국 **n-1**번 반복문을 호출한 것과 동일하며, 지역 변수 **n**이 **n-1**번 생성된다.
- **complexity** **:** O(n)
    - **time complexity :** O(n-1) = O(n)
    - **space complexity :** O(n-1) = O(n)

### 연습

```python
def n_sum(n):
	if n <= 1:
		return n
	else:
		return n + n_sum(n-1)
```