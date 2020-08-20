# Javascript - Operators

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용으로 Part 1에서 연산자에 대해 공부한 내용이다.

<br>

## 기본 연산자와 수학

자바스크립트에서만 제공하는 연산자에 대해 배운다.

<br>

### 용어: '단항', '이항', '피연산자'

* 피연산자(operand) s는 연산자가 연산을 수행하는 대상 `5 * 2` 에서는 `5, 2` 두 개의 피연산자다. '피연산자'는 '인수(argument)'라는 용어로 불리기도 한다.
* 피연산자를 하나만 받는 연산자는 단항(unary) 연산자라고 부른다. 피연산자의 부호를 뒤집는 단항 마이너스 연산자 `-`는 단항 연산자의 대표적인 예

```javascript
let x = 1;
x = -x;
alert(x); // -1
```

* 두 개의 피연산자를 받는 연산자는 이항(binary) 연산자라 부른다. 마이너스 연산자는 아래와 같은 이항 연산자로 쓸수 있다.

```javascript
let x = 1, y = 3;
alert(y-x); // 2
```

<br>

### 수학

> 어느 언어들과 다를 것이 없다.

* 덧셈(+), 뺄셈(-), 곱셈(*), 나눗셈(/), 나머지(%), 거듭제곱 연산자(**)

<br>

### 단항 연산자 +와 숫자형으로의 변환

덧셈 연산자 +는 이항 연산자뿐만 아니라 단항 연산자로도 사용할 수 있다.

```javascript
let x = 1;
alert(+x); // 1
let y = -2;
alert(+y); // -2
// 숫자형이 아닌 피연산자는 숫자형으로 변화
alert(+true); // 1
alert(+""); // 0
```

아래와 같이도 사용할 수 있다. 코드만 보고 넘어가자.

```javascript
let apples = "2";
let oranges = "3";

alert( apples + oranges ); // 23, 이항 덧셈 연산자는 문자열을 연결합니다.

// 이항 덧셈 연산자가 적용되기 전에, 두 피연산자는 숫자형으로 변화합니다.
alert( +apples + +oranges ); // 5

// `Number(...)`를 사용해서 같은 동작을 하는 코드를 작성할 수 있지만, 더 기네요.
// alert( Number(apples) + Number(oranges) ); // 5
```

<br>

### 증가/감소 연산자

* 증가 (increment) 연산자 : `++` 은 변수를 1 증가시킨다.
* 감소 (decrement) 연산자 : `--` 은 변수를 1 감소시킨다.

`++` 와 `--` 연산자는 변수의 앞이나 뒤에 올 수 있다.

* 후위형 (postfix form) : `counter++`와 같이 피연산자 뒤에 올 때를 말한다.
    * 새로운 값을 먼저 반환하고 피연산자를 증가시킨다.
* 전위형 (prefix form) : `++counter`와 같이 피연산자 앞에 올 때를 말한다.
    * 피연산자를 먼저 증가시키고 새로운 값을 반환한다.

<br>

### 비트 연산자

비트 연산자(bitwise operator)는 인수를 32비트 정수로 변환하여 이진 연산을 수행합니다.

* AND (&) , OR (|), XOR (^), NOT (~)
* LEFT SHIFT (<<), RIGHT SHIFT (>>), 부호 없는 오른쪽 시프트 (>>>)

<br>

### 쉼표 연산자

쉼표 연산자(`,`) 는 좀처럼 보기 힘들고 특이한 연산자 중 하나이다. 코드를 짧게 쓰려는 의도로 가끔 사용되는데, 쉼표 연산자는 여러 표현식을 코드 한 줄에서 평가할 수 있게 해준다. 이 때 표현식은 각각 모두 평가되지만, 마지막 표현식의 평가 결과만 반환된다.

```javascript
let a = (1 + 2, 3 + 4);
alert(a); // 5
```

> 쉼표의 우선 순위는 매우 낮다.

<br>

### 과제

#### 형변환

```javascript
"" + 1 + 0	// "" + 1 = "1" + 0 = "10"
"" - 1 + 0  // -1
true + false  // 1
6 / "3"	// 6 / 3 = 2 // / 는 숫자형만 인수로 받는다.
"2" * "3" // 2 * 3 = 6  // * 는 숫자형만 인수로 받는다.
4 + 5 + "px" // 4 + 5 = 9 + "px" = "9px"
"$" + 4 + 5 // $45
"4" - 2 // 2
"4px" - 2 // NaN
7 / 0 // Infinity
" -9  " + 5 // " -9  5"
" -9  " - 5 // -14
null + 1 // 1
undefined + 1 // NaN // undefined는 수치로 반환되지 않는다.
" \t \n" - 2
```

<br>

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

