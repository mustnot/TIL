# Javascript Tutorial 1 - 3

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용을 정리한 글이다. 정리할 내용은 다음과 같다.
>
> * Functions
> * Function expressions
> * Arrow functions, the basics

<br>

## 함수

스크립트를 작성하다 보면 유사한 동작을 하는 코드가 여러 곳에서 필요할 때가 있다. 함수는 프로그램을 구성하는 주요 '구성 요소(building block)'으로 함수를 이용하면 중복 없이 유사한 동작을 하는 코드를 여러 번 호출할 수 있다.

<br>

### 함수 선언

```javascript
function showMessage() {
    alert( 'this is message' )
}

showMessage();
```

```javascript
function name(parameters) {
    // 함수 본문
}
```

<br>

### 지역 변수

함수 내 선언한 변수는 지역 (local variable) 로 함수 안에서만 접근할 수 있다.

```javascript
function showMessage() {
    let message = "this is message!";
    alert( message );
}
showMessage()
alert( message ); // ReferenceError: message is not defined
```

<br>

### 외부 변수

함수 내부에서 함수 외부의 변수인 외부 변수 (outer variable) 에 접근할 수 있다.

```javascript
let userName = 'John';
function showMessage() {
    alert ( `Hello, ${userName}` );
}
showMessage();
```

함수에서는 외부 변수에 접근하는 것뿐만 아니라, 수정 역시도 가능하다.

```javascript
let userName = 'John';

function showMessage() {
	userName = "Bob"; // (1) 외부 변수를 수정함
	alert( `Hello, $(userName}` );
}
alert( userName ); // 함수 호출 전이므로 John 이 출력됨

showMessage();
alert( userName ); // 함수에 의해 Bob 으로 값이 바뀜
```

> ℹ️ 전역 변수
>
> 이러한 `userName` 과 같이 함수 외부에 선언된 변수는 전역 변수 (global variable) 라고 부르며, 이러한 전역 변수는 같은 이름을 가진 또 다른 지역 변수에 의해 변경되지 않는다면 모든 함수에서 접근할 수 있다.

<br>

### 매개변수

매개변수(parameter)를 이용하면 임의의 데이터를 함수 안에 전달할 수 있다. 매개변수는 인수(argument)라고 불리기도 하는데 실제로는 같진 않지만, 일단 넘어간다.

아래 예시는 `showMessage()` 함수에 매개변수 `from`과 `text`를 가지는 함수로 변경한 것이다.

```javascript
function showMessage(from, text) { // 인수: from, text
  alert(from + ': ' + text);
}

showMessage('Ann', 'Hello!'); // Ann: Hello! (*)
showMessage('Ann', "What's up?"); // Ann: What's up? (**)
```

<br>

### 함수 이름 짓기

> ℹ️ 함수는 동작 하나만 담당해야 한다.
>
> 함수는 함수 이름에 언급되어 있는 동작을 정확히 수행해야하고 그 외의 동작은 수행해서는 안된다. 독립적인 두 개의 동작은 독립된 함수 두 개에서 나눠서 수행할 수 있게 해야 하고, 만약 두 동작을 필요로 하는 경우에는 제 3의 함수를 만들어 그 곳에서 두 함수를 호출한다.
>
> * `getAge` 함수는 나이를 얻어오는 동작만 수행해야 하는데, `alert` 창에 나이를 출력해주는 동작은 이 함수에 들어가지 않는 것이 좋다.
> * `createForm` 함수는 `form`을 만들고 이를 반환하는 동작만 수행 해야 하고, `form`을 문서에 추가하는 동작이 해당 함수에 들어가 있으면 좋지 않다.

함수는 어떤 `동작`을 수행하기 위한 코드를 모아놓은 것으로 함수의 이름을 대개 동사입니다. 그렇기에 함수 이름은 가능한 한 간결하고 명확해야한다. 함수가 어떤 동작을 하는지 설명할 수 있어야하기 때문인데, 코드를 읽는 사람은 함수 이름만 보고도 함수가 어떤 기능을 하는지 힌트를 얻을 수 있어야한다.

함수가 어떤 동작을 하는지 축약해서 설명해주는 동작을 접두어로 붙여 함수 이름을 만드는 게 관습이지만, 팀 내 합의된 접두어만 사용하는 것이 좋다.

```javascript
showMessage(..)     // 메시지를 보여줌
getAge(..)          // 나이를 나타내는 값을 얻고 그 값을 반환함
calcSum(..)         // 합계를 계산하고 그 결과를 반환함
createForm(..)      // form을 생성하고 만들어진 form을 반환함
checkPermission(..) // 승인 여부를 확인하고 true나 false를 반환함
```

<br>

<br>

## 함수 표현식

자바스크립트는 함수를 특별한 종류의 값으로 취급한다. 지금까지 배운 함수는 `함수 선언문` 방식으로 함수를 만들었지만, `함수 표현식` 방식도 존재한다.

```javascript
// 함수 선언문
function sayHi() {
    alert("Hello!");
}
// 함수 표현식
let sayHi = function() {
    alert("Hello!");
};
```

<br>

### 콜백 함수

함수를 값처럼 전달하는 예시, 함수 표현식에 관한 예시를 조금 더 살펴보면 예로 매개변수가 3개가 있는 함수, `ask(question, yes, no)` 를 작성한다고 할 때 각 매개변수에 대한 설명은 다음과 같다.

* `question` : 질문
* `yes` : `yes`라고 대답한 경우 실행하는 함수
* `no` : `no`라고 답한 경우 실행되는 함수

함수는 반두시 `question`을 해야 하고, 사용자의 답변에 따라 `yes(), no()` 를 호출한다.

```javascript
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

function showOk() {
  alert( "동의하셨습니다." );
}

function showCancel() {
  alert( "취소 버튼을 누르셨습니다." );
}

// 사용법: 함수 showOk와 showCancel가 ask 함수의 인수로 전달됨
ask("동의하십니까?", showOk, showCancel);
```

이런 함수 작성 방식은 실무에서 아주 유용하게 쓰이는데, 여기서 `ask`의 인수인, `showOk`와 `showCancel`를 **콜백 함수** 또는 **콜백** 이라고 부릅니다. 위 코드를 아래와 같이 짧게 만들 수도 있다.

```javascript
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

ask(
  "동의하십니까?",
  function() { alert("동의하셨습니다."); },
  function() { alert("취소 버튼을 누르셨습니다."); }
);
```

> ℹ️ 함수 선언문과 함수 표현식 중 무엇을 선택해야 하나요?
>
> 저자의 경험에 따르면 함수 선언문을 이용해 함수 선언하는 걸 먼저 고려하는 게 좋다. 함수 선언문으로 함수를 정의하면, 함수가 선언되기 전에 호출할 수 있어서 코드 구성을 좀 더 자유롭게 할 수 있다. (실행 코드를 함수보다 위에 작성할 수 있다.)
>
> 함수 선언문은 가독성 역시 좋은데, `let f = function(...) {...}` qㅗ다 `function f(...) {...}` 를 찾는 게 더 쉽다.

<br>

<br>

## 화살표 함수 기초

> python의 lambda 같은건가..

함수 표현식보다 더 단순하고 간결한 문법으로 함수를 만들 수 있는 방법이 있는데, 바로 화살표 함수 (arrow function)을 사용하는 것이다.

```javascript
let func = (arg1, arg2, ...) => expression
```

화살표 함수는 아래와 같은 방법으로도 사용할 수 있다. 하지만 처음 접하거나 익숙하지 않은 개발자에게는 가독성이 매우 떨어진다.

```javascript
let age = prompt("나이를 알려주세요.", 18);

let welcome = (age < 18) ?
  () => alert('안녕') :
  () => alert("안녕하세요!");

welcome();
```

<br>

### 본문이 여러 줄인 화살표 함수

위에서 사용한 화살표 함수들은 주로 한 줄로 작성되어 하나의 표현식을 사용하는 함수들이었는데, 만약에 표현식이나 구문이 여러 개인 함수가 있을 수 있다면, 어떻게 해야할까? 이 경우 역시 화살표 함수 문법을 사용해 함수를 만들 수 있는데, 다만 중괄호 안에 코드를 넣어줘야하고 `return` 지시자를 사용해 명시적으로 결과값을 반환해 주어야한다.

```javascript
let sum = (a, b) => {
    let result = a + b;
    return result;
};
alert(sum(1, 2))
```

