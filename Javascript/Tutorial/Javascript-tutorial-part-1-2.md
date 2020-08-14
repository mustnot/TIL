# Javascript Tutorial 1 - 2

> ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용을 정리한 글이다. 정리할 내용은 다음과 같다.
>
> * 조건부 연산자 if 와 ?
> * 논리 연산자
> * null 병합 연산자 '??'
> * while과 for 반복문
> * switch문

<br>

## 조건부 연산자 `if`와 `?`

`if (...)`문은 괄호 안에 들어가는 조건의 결과가 `true`이면 아래 명시된 코드 블록이 실행된다. 공부한 내용을 종합적으로 하나의 코드로 작성하면 다음과 같다.

```javascript
if (year >= 2021) {
    alert("future");
} else if (year >= 2020) {
    alert('present');
} else {
    alert('past');
}
```

```javascript
let age = prompt('나이를 입력해 주세요.', '');

// using if statement
let accessAllowed = false;
if (age > 18) {
    accessAllowed = true;
}

// using ? statement
// let result = condition ? value1 : value2;
let accessAllowed = (age > 18) ? true : false;
```

<br>

<br>

## 논리 연산자

자바스크립트엔 세 종류의 논리 연산자 `||` (OR), `&&` (AND), `!` (NOT) 이 있습니다.

### OR 연산자 - `||`

전통적인 프로그래밍에서 OR 연산자는 불린값을 조작하는 데 쓰인다. 인수 중 하나라도`true` 이면 `true` 를 반환하고, 그렇지 않으면 `false` 를 반환한다. 자바스크립의 OR 연산자는 다루긴 까다롭지만 강력한 기능을 제공한다.

```javascript
// OR
alert ( true || true );		// true
alert ( true || false);		// true
alert ( false || false);	// false
```

<br>

#### 첫 번째 truthy를 찾는 OR 연산자 `||`

논리연산자 OR의 '추가' 기능으로 다음과 같은 알고리즘으로 동작한다.

```javascript
result = value1 || value2 || value3;
/*
if (value1) {
	result = value1;
} else if (value2) {
	result = value2;
}	else if (value3) {
	result = value3;
}	else {
	result = false;
}
*/
```

이런 기능을 이용하여 여러 용도로 OR 연산자를 활용할 수 있다.

**1. 변수 또는 표현식으로 구성된 목록에서 첫 번째 truthy 얻기**

> 이건 나중에 사용하기 좋은 예제인 것 같다.

```javascript
let firstName = "";
let lastName = "";
let nickName = "바이올렛";

alert( firstName || lastName || nickName || "익명"); // 바이올렛
```

**2. 단락평가**

OR 연산자 `||`가 제공하는 또 다른 기능은 '단락 평가(short circuit evaluation)' 이다. 위와 같이 OR 연산자는 왼쪽에서 시작해서 오른쪽으로 평가를 진행하는데, truthy를 만나면 나머지 값들은 건드리지 않은 채 평가를 멈춘다. 이런 프로세스를 단락 평가라고 한다.

```javascript
result = prompt("이름을 입력하세요", "") || alert("이름을 입력하지 않았습니다.")
```

<br>

### AND 연산자 - `&&`

AND 연산자는 OR 연산자와 정반대로 생각하면 안되지만, 대체로 정반대로 생각하면 편한 것 같다. OR 연산자가 첫 번째 truthy를 찾는데 사용했다면, 반대로 AND 연산자는 첫 번째 falsy를 찾는데에 사용한다. 아래 예시 코드를 정리하고 넘어간다.

```javascript
// AND
alert ( true && true);		// true
alert ( true && false);		// false
alert ( false && false);	// false
```

```javascript
result = value1 && value2 && value3
/*
if ( !value1 ) {
	result = value1;
} else if ( !value2 ) {
	result = value2;
} else if ( !value3 ) {
	result = value3;
} else {
	result = true;
}
*/
```

<br>

<br>

## null 병합 연산자 `??`

> ⚠️ 최근에 추가됨
>
> 스펙에 추가된 지 얼마 안 된 문법으로 구식 브라우저는 폴리필이 필요합니다.

null 병합 연산자 (nullish coalescing operator) `??`를 사용하면 여러 피연산자 중 그 값이 '확정되어 있는' 변수를 짧은 문법으로도 찾을 수 있다. 상황에 따른 `a ?? b`의 결과를 보면 다음과 같다.

* `a`가 `null`이나 `undefined`가 아니면 `a`
* `a`가 `null`이나 `undefined`가 아니면 `b`

```javascript
x = (a !== null && a !== undefined) ? a : b;
```

```javascript
let firstName = null;
let lastName = null;
let nickName = "바이올렛";

// null이나 undefined가 아닌 첫 번째 피연산자
alert(firstName ?? lastName ?? nickName ?? "익명"); // 바이올렛
```

<br>

#### `??` 와 `||` 의 차이

null 병합 연산자와 OR 연산자는 실제로 상당히 유사해보이는데, 위의 코드를 수정하더라도 결과는 동일하게 나타난다. 하지만 중요한 차이점이 있는데, 코드를 통해 확인하자.

```javascript
let height = 0;

alert(height || 100); // 100
alert(height ?? 100); // 0
```

`??` 연산자 같은 경우 어쨋든 `false` 더라도 할당되어 있다면 그 값을 리턴하는 반면 `||` 연산자는 정확히 값이 `true` 이여야 반환한다.

<br>

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

