# Javascript Tutorial 2

> [모던 Javascript 튜토리얼](https://ko.javascript.info/) 을 통해 공부 중입니다.

<br>

## 변수와 상수

대다수 자바스크립트 어플리케이션은 사용자나 서버로부터 입력받은 정보를 처리하는 방식으로 동작한다.

* 온라인 쇼핑몰 - 판매 중인 상품 혹은 장바구니 등의 정보
* 채팅 어플리케이션 - 사용자 정보, 메시지 등의 정보

변수는 이러한 정보를 저장하는 용도로 사용한다.

<br>

### 변수 (variable)

자바스크립트에서는 `let` 키워드를 사용해 변수를 생성합니다.

```javascript
let message;
message = 'hello';
alert(message);
```

<br>

한 줄에 여러 개의 변수를 설정할 수 있으나, 되도록이면 가독성을 위해 여러 줄로 분리한다.

```javascript
let user = 'John', age = 25, message = 'hello';
```

```javascript
let user = 'John';
let age = 25;
let message = 'hello';
```

> ℹ️ let 대신 var
>
> ```javascript
> var message = 'hello';
> ```
>
> 와 같이 `var`를 이용한 방법도 있으나, 오래된 방식으로 거의 동일한 `let`을 사용하자

<br>

### 변수 명명 규칙

자바스크립트에서 변수 명명 시에는 두 가지 제약 사항이 있다.

1. 변수명에는 오직 문자와 숫자, 그리고 기호 $ 와 _ 만 들어갈 수 있다.
2. 첫 글자는 숫자가 될 수 없다.

여러 단어를 조합해서 변수명을 만들 때에는 [카멜 표기법(camelCase)](https://en.wikipedia.org/wiki/CamelCase)가 흔히 사용되는데, 카멜 표기법은 단어를 차례대로 나열하면서 첫 단어를 제외한 각 단어의 첫 글자를 대문자로 작성한다. `myVeryLongName`과 같이 말이다.

> ⚠️ use strict 없이 변수 할당하기
>
> 변수는 대게 정의되어 있어야 사용할 수 있으나 예전에는 `let` 없이도 단순하게 값을 할당해 변수를 생성하는 것이 가능했기 때문인데, 이 때 앞서 배운 `"use strict"` 를 쓰지 않으면 과거 스크립트와의 호환성을 유지할 수 있기 때문에 이 방식을 사용할 수 있다.
>
> ```javascript
> num = 5;
> alert(num);
> ```
>
> 하지만 이렇게 변수를 생성하는 것은 나쁜 관습으로 엄격 모드에서는 에러를 발생시킨다.
>
> ```javascript
> "user strict";
> num = 5;
> ```

<br>

### 상수 (constant)

변화하지 않는 변수를 선언할 때에는 `let` 대신에 `const`를 사용한다.

```javascript
const myBirthday = '2020.01.01'
```

이렇듯 생일과 같이 변화하지 않는 변수를 선언할 때에는 `const`를 사용하며 상수라고 부른다. 상수는 재할당을 할 수 없으므로 상수를 변경하려 하면 에러를 발생시킨다.























