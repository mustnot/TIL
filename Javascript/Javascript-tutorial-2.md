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

### 과제 1 : 변수 가지고 놀기

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

### 과제 2 : 올바른 이름 선택하기

1. 현재 우리가 살고 있는 행성(planet)의 이름을 값으로 가진 변수를 만들고, 변수 이름 역시 설정 하시오.
2. 웹사이트를 개발 중이라고 가정하고, 현재 접속 중인 사용자(user)의 이름(name)을 저장하는 변수를 만들고, 변수 이름을 설정 하시오.

```javascript
let outPlanet = "Earth";
```

```javascript
let currentUserName = "whoareyou";
```

<br>

### 과제 3 : 대문자 상수 올바르게 사용하기

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

