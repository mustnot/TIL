# Javascript Tutorial 3

> [모던 Javascript 튜토리얼](https://ko.javascript.info/) 을 통해 공부 중입니다.

<br>

## alert, prompt, confirm 을 이용한 상호작용

브라우저 환경에서 사용되는 최소한의 사용자 인터페이스 기능인 `alert`, `prompt`, `confirm`에 대해 알아본다.

<br>

#### alert

이 함수가 실행되면 사용자가 '확인(OK)' 버튼을 누를 때까지 메세지를 보여주는 창이 계속 떠있게 된다.

<br>

#### prompt

브라우저에서 제공하는 `prompt` 함수는 두 개의 인수를 받는다. 실행되면 텍스트 메시지와 입력 필드(input field), 확인(OK) 및 취소(Cancle) 버튼이 있는 모달 창을 띄워준다.

```javascript
result = prompt(title, [default]);
```

* `title` : 사용자에게 보여줄 문자열
* `default` : 입력 필드의 초기값 (선택값)

> ℹ️ 인수를 감싸는 대괄호 `[...]` 의 의미
>
> 매개변수가 필수가 아닌 선택값이라는 것을 의미한다.

사용자는 프롬프트 대화상자의 입력 필드에 원하는 값을 입력하고 확인을 누를 수 있고, 값을 입력하길 원치 않는 경우 취소나 `Esc` 를 통해 빠져나갈 수 있습니다.

`prompt` 함수는 사용자가 입력 필드에 기재한 문자열을 반환하고 사용자가 입력을 취소한 경우 `null`이 반환된다.

```javascript
let age = prompt('나이를 입력하시오.', 100);
alert(`당신의 나이는 ${age}살 입니다.`); // 당신의 나이는 100살 입니다.
```

<br>

#### confirm

```javascript
result = confirm(question);
```

`confirm` 함수는 매개변수로 받은 `question`과 확인 및 취소 버튼이 있는 모달 창을 보여준다.

사용자가 확인을 누르면 `true`, 그 외에는 `false`를 반환한다.

```javascript
let isAdmin = confirm('Are you Admin?');
alert(isAdmin);
```

<br>

### 과제

#### 간단한 페이지 만들기

사용자에게 이름을 물어보고, 입력받은 이름을 그대로 출력해주는 페이지를 만들어 보세요.

```javascript
// getname.js
"use strict";

let name;
name = prompt('What is your name?', '');
document.write(name);
```

```html
<!DOCTYPE html>
<html lang="en">
<body>
    <script src="getname.js"></script>
</body>
</html>
```

<br>

<br>

## 형 변환 (type conversion)

함수와 연산자에 전달되는 값은 대부분 적절한 자료형으로 자동 변환되는데, 이런 과정을 "형 변환"이라고 한다. 예를 들어 `alert`가 전달 받은 값의 자료형과 관계없이 이를 문자열로 자동 변환하여 보여주는 것이나, 수학 관련 연산자가 전달받은 값을 숫자로 변환하는 경우가 대표적인 형 변환의 예이다.

이 외에, 전달 받은 값을 의도에 맞게 원하는 타입으로 변환 하는 것 역시 형 변환이라 할 수 있다.

<br>

#### String : 문자형으로 변환

```javascript
let value = true;
alert(typeof value); // boolean

value = string(value);
alert(type of value); // string
```

<br>

#### Number : 숫자형으로 변환

```javascript
let str = "123";
alert(typeof value); // string

value = number(str);
alert(typeof value); // number
```

* `undefinded` : `NaN`
* `null` : `0`
* `true and false` : `1 and 0`
* `string` : 문자열의 처음과 끝 공백이 제거되고 남아있는 문자열이 없다면 `0`, 그렇지 않다면 숫자를 읽는데, 실패할 경우 `NaN`을 리턴한다.

<br>

#### Boolean : 불린형으로 변환

`0`, `''` , `""` , `undefined`, `NaN` 과 같이 직관적으로 비어있거나 존재하지 않는 값에 대해서는 `false`를 리턴한다.

```javascript
alert( Boolean(1) ); // 숫자 1(true)
alert( Boolean(0) ); // 숫자 0(false)

alert( Boolean("hello") ); // 문자열(true)
alert( Boolean("") ); // 빈 문자열(false)
```





