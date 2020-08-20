# Javascript - Loops

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용으로 Part 1의 내용 중 Loop 반복문을 공부하고 정리한 글이다.

<br>

## 반복문 while 과 for

> 문법만 보고 넘어가자

<br>

#### `while` 반복문

```javascript
while (condition) {
    // body
}
```

#### `do...while` 반복문

`do...while` 문법을 사용하면 `condition`을 반복문 아래로 옮길 수 있는데, 이 때 본문이 먼저 실행되고, 조건을 확인한 후 조건이 `true`인 동안 본문이 계속 실행된다.

```javascript
do {
    // body
} while (condition);
```

#### `for` 반복문

```javascript
for (begin; condition; step) {
    // body
}
```

```javascript
for (let i = 0; i < 3; i++) { // 0, 1, 2가 출력됩니다.
  alert(i);
}
```

```javascript
for (let i = 2; i <= 10; i+=2) {
    console.log(i)
}
```

<br>

<br>

## switch 문

복수의 `if` 조건문은 `switch` 문으로 바꿀 수 있는데, `switch` 문을 사용한 비교법은 특정 변수를 다양한 상황에서 비교할 수 있게 해준다. 코드 자체가 비교 상황을 잘 설명한다는 장점도 있다. `switch` 문은 하나 이상의  `case` 문으로 구성된다. 대게 `default` 문도 있지만 필수는 아니다.

```javascript
// example
switch (x) {
    case 'value1':
        // ...
        break;
    case 'value2':
        // ...
        break;
    case 'value3':
        // ...
        break;
    default:
        // ...
        break;
}
```

- 변수 `x`의 값을 순서대로 작성한 `case` 에 작성한 `value` 와 비교한다.
- `case` 문에서 변수 `x` 와 값이 일치하는 값을 찾으면 `case` 문의 아래 코드가 실행되며 이때, `break` 문을 만나거나 `switch` 문이 끝나면 실행이 멈춥니다.
- 값과 일치하는 `case`문이 없다면, `default` 문 아래의 코드가 실행되는데, 이는 필수가 아니기 때문에 없는 경우 종료된다.

<br>

#### 여러 개의 `case` 문 묶기

```javascript
let a = 3;

switch (a) {
  case 4:
    alert('계산이 맞습니다!');
    break;

  case 3: // (*) 두 case문을 묶음
  case 5:
    alert('계산이 틀립니다!');
    alert("수학 수업을 다시 들어보는걸 권유 드립니다.");
    break;

  default:
    alert('계산 결과가 이상하네요.');
}
```

