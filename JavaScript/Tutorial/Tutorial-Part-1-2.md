# Javascript Tutorial Part 1 - 2

> ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용을 정리한 글로 "자바스크립트 기초" 편을 정리한 내용입니다.

<br>

## Hello, World!

진행 중인 튜토리얼은 온라인으로 제공되기 때문에 실행환경을 브라우저로 한정하며 `Node.js`와 같이 브라우저 이외에 환경에 주력하는 학습자를 위해, 브라우저 한정 명령어 (alert 등)는 최소한으로 사용하여 안내한다. 

<br>

### `script` 태그

`<script>` 태그를 이용하면 자바스크립트 프로그램을 HTML 문서 어느 곳에나 삽입할 수 있습니다.

```html
<!DOCTYPE html>
<html lang="en">
<body>
    <p>스크립트 전</p>
    <script>
        alert( 'Hello, world!' );
    </script>
    <p>스크립트 후</p>
</body>
</html>
```

<br>

### 모던 마크업

`<script>` 태그엔 몇 가지 속성(attribute)이 있다. 요즘에는 잘 사용하지 않지만 오래된 코드에서 종종 볼 수 있다.

* type 속성 : `<script type=...>`

    * HTML4 에서는 스크립트에 `type`을 명시하는 것이 필수였다. 따라서 `type="text/javascript"` 속성이 붙은 스크립트를 어렵지 않게 찾을 수 있는데, 이제는 타입 명시가 필수가 아니다. 모던 HTML 표준에선 이 속성의 의미가 바뀌었는데, 이 속성은 자바스크립트 모듈에 사용할 수 있지만 다음에 배운다.

* language 속성 : `<script language=...>`

    * 현재 사용하고 있는 스크립트 언어를 나타내지만, 지금은 자바스크립트가 기본 언어이기 때문에 더는 사용할 필요가 없다.

* 스크립트 전후에 위치한 주석

    * 아주 오래된 책과 가이드에서는 다음과 같이 `<script>` 태그 안에 주석이 존재하는 것을 볼 수 있다.

        ```html
        <script type="text/javascript"><!--
            ...
            //--></script>
        ```

<br>

### 외부 스크립트

자바스크립트 코드의 양이 많은 경우에는 파일로 소분하여 저장할 수 있는데, 이렇게 분해해 놓은 각 파일은 `src` 속성을 사용하여 HTML에 삽입합니다.

```html
<script src="/path/to/script.js>"</script>
```

`src`의 경로는 <u>절대경로</u>를 나타내기도 하고 <u>상대경로</u>를 사용하는 것도 가능하다. 또는 URL 전체를 속성으로 사용할 수도 있습니다.

```html
<script src="https://cdnjs.cloudflare.com/ajax/lib/lodash.js/3.2.0/lodash.js"></script>
```

> 🚨 script에 src 속성이 있으면 태그 내부의 코드는 무시된다.

<br>

### 과제

#### alert 창 띄우기

```html
# alert.html
<!DOCTYPE html>
<html lang="en">
<body>
    <script>
        alert('자바스크립트!');
    </script>
</body>
</html>
```

<br>

#### 외부 스크립트를 이용해 alert 창 띄우기

> 한글이 깨져서 당황했다. encoding 문제인 것 같아 charset을 추가했다.

```javascript
// alert.js
alert('자바스크립트!');
```

```html
<!-- alert.html -->
<!DOCTYPE html>
<html lang="en">
<body>
    <script src="alert.js" charset="utf-8"></script>
</body>
</html>
```

<br>

<br>

## 코드 구조

코드 블록을 만드는 방법을 설명한다. 

<br>

### 문 (statement)

statement는 어떤 작업을 수행하는 문법 구조(syntax structure)와 명령어(command)를 의미합니다. 코드에는 원하는 만큼의 문을 작성할 수 있는데, 이 때 서로 다른 문은 세미콜론(;)으로 구분한다. 그리고 코드의 가독성을 높이기 위해 각 문은 서로 다른 줄에 작성하는 것이 일반적이다.

```script
alert('hello_1');
alert('hello_2');
```

<br>

### 세미콜론 (;)

줄바꿈이 있다면 세미콜론(;)을 생략할 수 있어, 위 코드에서 `;`을 제거하더도 에러 없이 동작한다. 그 이유는 자바스크립트는 암시적으로 줄바꿈을 세미콜론으로 해석하는데, 이런 동작 방식을 "세미콜론 자동 삽입"이라 부른다. 대부분의 경우, 줄바꿈은 세미콜론을 의미하나 "대부분의 경우"이지 "항상"을 의미하진 않는다.

```javascript
alert(3 +
     1
     + 2);
```

이 같은 경우에는 줄바꿈을 세미콜론으로 이해하면 안된다. 반면, 세미콜론이 정말로 필요하지만 자바스크립트가 이를 추정하지 "못하는" 상황도 존재한다.

<br>

### 주석

**1줄 주석**

```javascript
// 이 주석은 한 줄 주석
```

**여러줄 주석**

```javascript
/* 두 줄 이상 주석 예제
이것은 주석입니다.
*/
```

<br>

### 엄격 모드

자바스크립트는 꽤 오랫동안 호환성 이슈 없이 발전해왔으며 기존의 기능을 변경하지 않으면서 새로운 기능들이 추가되었다. 덕분에 기존에 작성한 코드는 절대 망가지지 않는다는 장점이 있었다. 하지만 자바스크립트 창시자들이 했던 실수나 불완전한 결정이 언어 안에 영원히 박제된다는 단점이 있다.

이런 상황은 ECMAScript5(ES5)가 등장하기 전인 2009년까지 지속되었고, 새롭게 제정된 ES5에서는 새로운 기능이 추가되고 기존 기능 중 일부가 변경되었다. 기존 기능을 변경하였기 때문에 하위 호환성 문제는 여지 없이 발생했고 그래서 변경사항 대부분은 ES5의 기본 모드에선 활성화되지 않도록 설계되었다. 대신 `use strict`라는 특별 지시자를 사용해 엄격 모드(strict mode)를 활성화 했을 때만 이 변경사항이 활성화되게 해놓았다.

<br>

#### Use Strict

지시자 `"use strict"` 혹은 `'use stric'`은 단순한 문자열처럼 생겼지만. 이 지시자가 스크립트 최상단에 오면 스크립트 전체가 "모던한" 방식으로 동작된다.

```javascript
"use strict";
```

명령어를 그룹화하는 방식인 함수에 대해선 곧 학습할 예정으로 함수에 대해 학습하기 전 "use strict"는 스크립트 최상단이 아닌 함수 본문 맨 앞에 올 수 있다는 점을 기억하자. 이렇게 하면 오직 해당 함수만 엄격 모드로 실행된다.

> ⚠️ use strict를 취소할 방법은 없다.

<br>

#### 'use strict'를 꼭 사용해야 하는가?

모던 자바스크립트는 '클래스'와 '모듈'이라 불리는 진일보한 구조를 제공하는데, 이 둘을 사용하면 use strict가 자동으로 적요요된다. 따라서 이 둘을 사용하고 있다면 스크립트에 "use strict"를 붙일 필요가 없다. 하지만 사용하지 않는다면, "use strict"를 항상 사용하도록 한다.

<br>

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
const myBirthday = '2020.01.01';
```

이렇듯 생일과 같이 변화하지 않는 변수를 선언할 때에는 `const`를 사용하며 상수라고 부른다. 상수는 재할당을 할 수 없으므로 상수를 변경하려 하면 에러가 발생한다.

```javascript
const myBirthday = '2020.01.01';
myBirthday = '2020.01.02';
```

<br>

### 대문자 상수

기억하기 힘든 값을 변수에 할당하여 별칭으로 사용하는 것은 널리 사용되는 관습이다. 이러한 상수는 대문자와 밑줄로 구성된 이름으로 명명한다. 글로벌 변수와 동일하다.

```javascript
const COLOR_RED = '#F00';
const COLOR_GREEN = '#0F0';
// 색상을 고를 때에는 `let`을 사용하여 할당
let color = COLOR_ORANGE;
```

<br>

### 바람직한 변수명

변수명은 간결하고, 명확해야한다. 다른 개발자가 보더라도 변수명을 보고 이것이 무엇을 의미하는지 잘 설명할 수 있어야한다. 변수의 이름을 짓는 것은 개발에서 가장 중요하고 복잡한 기술 중 하나이다. 실제 프로젝트에서 처음부터 완전히 독립적인 코드를 개발하기 보다는 기존 코드의 틀을 변경하고 확장하면서 대부분의 시간을 할당하는데, 작성했던 코드를 얼마 후에 다시 봤을 때, 정보에 알맞은 이름이 적혀 있으면 정보를 더 쉽게 찾을 수 있다.

**좋은 명명 규칙**

* `userName` 이나 `shoppingCart` 와 같이 사람이 읽을 수 있는 이름을 사용
* 무엇을 하고 있는지 명확히 알고 있지 않을 경우에는 줄임말이나 `a, b, c` 와 같은 짧은 단어는 피한다.
* 최대한 서술적이고 간결하게 명명한다. 대표적인 예로 `data`와 `value`이다. 코드 문맥상 변수가 가리키는 데이터나 값이 아주 명확할 때에만 이러한 이름을 사용한다.
* 자신만의 규칙 혹은 소속된 팀의 규칙을 따르자.

> ℹ️ 변수는 재사용하는 것이 좋을까 새로 만드는 것이 좋을까?
>
> 개발자 중 새로운 변수를 선언하기보다 기존 변수를 재사용 하는 것을 선호하는 사람들이 있는데, 재사용된 변수는 과거에 붙여진 스티커를 떼지 않은 채 물건만 바뀐 상자와 같다. (만약 독립된 제품일 경우에는 가능할까?) 변수를 재사용하면 변수 선언에 쏟는 노력을 덜 순 있어도 디버깅에 열 배 더 많은 시간을 쏟아야한다. 예시로 `data`라는 변수명을 쓰이는 곳이 이곳 저곳 다양하다면, 관련된 변수명을 수정하거나 어떤 값에서 에러가 났는지 정확히 판단하기 어려움이 있다.

<br>

### 과제

#### 1. 변수 가지고 놀기

1. `admin`과 `name`이라는 변수 선언
2. `name`에 값으로 `John`을 할당
3. `name`의 값을 `admin`에 복사
4. `admin`의 값을 `alert`창에 띄워보고 `John`으로 출력되는지 확인하라.

```javascript
"use strict";
let admin, name;

name = "John";
admin = name;

alert(admin);
```

<br>

#### 2. 올바른 이름 선택하기

1. 현재 우리가 살고 있는 행성(planet)의 이름을 값으로 가진 변수를 만들고, 변수 이름 역시 설정 하시오.
2. 웹사이트를 개발 중이라고 가정하고, 현재 접속 중인 사용자(user)의 이름(name)을 저장하는 변수를 만들고, 변수 이름을 설정 하시오.

```javascript
let outPlanet = "Earth";
```

```javascript
let currentUserName = "whoareyou";
```

<br>

#### 3. 대문자 상수 올바르게 사용하기

아래 코드를 평가하시오

```javascript
const birthday = '18.04.1982';
const age = getAge(birthday);
```

`birthday`는 태어난 이상 변경되지 않기 때문에 대문자여도 좋지만, `age`는 매년 변경되기 때문에 소문자로 놓는 것이 옳다.

```javascript
const BIRTHDAY = '18.04.1982';
const age = getAge(BIRTHDAY);
```

<br>

<br>

## 자료형

자바스크립트에서 값은 항상 문자열이나 숫자형 같은 특정한 자료형에 속하며 총 여덟 가지 기본 자료형이 있습니다. 

<br>

- `숫자형` – 정수, 부동 소수점 숫자 등의 숫자를 나타낼 때 사용합니다. 정수의 한계는 ±253 입니다.

```javascript
let n = 123;
n = 12.345;
n = 1 / 0; // 무한대
n = Infinity; // 무한대
```

- `bigint` – 길이 제약 없이 정수를 나타낼 수 있습니다. 끝에 n이 붙는다.

```javascript
const bigInt = 1234567890123456789012345678901234567890n;
```

- `문자형` – 빈 문자열이나 글자들로 이뤄진 문자열을 나타낼 때 사용합니다. 단일 문자를 나타내는 별도의 자료형은 없습니다.

```javascript
let str = "hello";
let str = "this is string";
```

```javascript
let phrase = `can embed another ${str}`;	// ' / " 이 아닌 ` 을 사용한다.
// example
let name = "John";
alert( `Hello, ${name}!`);
```

- `불린형` – `true`, `false`를 나타낼 때 사용합니다.

```javascript
let nameFieldChecked = true;
let ageFieldChecked = false;
```

- `null` – `null` 값만을 위한 독립 자료형입니다. `null`은 알 수 없는 값을 나타냅니다.

```javascript
let age = null;
```

- `undefined` – `undefined` 값만을 위한 독립 자료형입니다. `undefined`는 할당되지 않은 값을 나타냅니다.

```javascript
let age;
alert(age); // 'undefined' 출력되며 age가 아직 정의되지 않았음을 의미한다.
```

- `객체형` – 복잡한 데이터 구조를 표현할 때 사용합니다. (다시 설명 예정)

객체형은 특수한 자료형으로 객체형을 제외한 다른 자료형은 문자열이든 숫자든 한 가지만 표현할 수 있기 때문에 원시(primitive) 자료형이라 부르지만, 객체는 데이터 컬렉션이나 복잡한 개체(entity)를 표현할 수 있습니다.

이런 특징 때문에 자바스크립트에서 객체는 좀 더 특별한 취급을 받고 있습니다.

- `심볼형` – 객체의 고유 식별자를 만들 때 사용합니다. (다시 설명 예정)

<br>

### typeof 연산자

`typeof` 연산자는 인수의 자료형을 반환하고 자료형에 따라 처리 방식을 다르게 하고 싶거나 변수의 자료형을 빠르게 알아내고자 할 때 유용하다.

* `typeof x` vs `typeof(x)` : 각각 연산자로 쓰임과 함수형으로 쓰이는 형태로 둘다 동일하다.

```javascript
typeof undefined // "undefined"

typeof 0 // "number"

typeof 10n // "bigint"

typeof true // "boolean"

typeof "foo" // "string"

typeof Symbol("id") // "symbol"

typeof Math // "object"  (1)

typeof null // "object"  (2)

typeof alert // "function"  (3)
```

<br>

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

<br>

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

<br>

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

