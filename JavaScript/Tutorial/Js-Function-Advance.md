# Javascript Tutorial - Function Advanced

> ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용으로 Part 1의 함수 심화학습 부분에 대해 공부하고 정리한 내용입니다.

<br>

## 변수의 유효범위와 클로저

자바스크립트는 함수 지향 언어이다. 이런 특징은 개발자에게 많은 자유도를 주는데, 함수를 동적으로 생성할 수 있고, 생성한 함수를 다른 함수에 인수로 넘길 수 있으며, 생성된 곳이 아닌 곳에서 함수를 호출할 수도 있기 때문이다.

> ℹ️ 여기서부터는 `let`과 `const`로 선언한 변수만 다룬다.
>
> 자바스크립트에서는 3개의 키워드를 사용해 변수를 선언할 수 있는데, 모던한 방식으로는 `let`, `const`가 있고, 과거의 잔재인 `var`도 있습니다.

<br>

### 중첩 함수

함수 내부에서 선언한 함수는 `중첩(nested)` 함수라고 부릅니다. 자바스크립트에서는 중첩 함수를 손쉽게 만들 수 있는데, 아래와 같이 코드를 정돈하는데 사용할 수 있습니다.

예시 :

```javascript
function sayHiBye(firstName, lastName) {
    function getFullName() {
        return firstName + " " + lastName; 
    }
    let fullName = getFullName();
    alert( "Hello! " + fullName );
    alert( "Bye! " + fullName );
}
```

위 예시는 외부 변수에 접근해 이름 전체를 반환해주는 중첩 함수 `getFullName()`은 편의상 만든 함수이다. 이런 식으로 자바스크립트에서는 중첩 함수를 흔히 볼 수 있다.

중첩 함수는 반환될 수 있다는 점에서 매우 흥미롭다. 새로운 객체의 프로퍼티 형태로나 중첩 함수 그 자체로 반환된다. 이렇게 반환된 함수는 어디서든 호출해 사용할 수 있다. 물론 외부 변수에 접근할 수도 있다.

아래 함수 `makeCounter`는 숫자를 세주는 '카운터' 함수로 호출될 때마다 다음 숫자를 반환환다.

```javascript
function makeCounter() {
    let count = 0;
    return function() {
        return count++;
    };
}

let counter = makeCounter();

console.log( counter() ); // 0
console.log( counter() ); // 1
console.log( counter() ); // 2
```

<br>

### 렉시컬 환경

> ⚠️ 매우 어려움 주의

#### 단계 1. 변수

자바스크립트에선 실행 중인 함수, 코드 블록 `{...}`, 스크립트 전체는 <u>렉시컬 환경(Lexical Environment)</u>이라 불리는 <u>내부 숨김 연관 객체(internal hidden associated object)</u>를 갖는다.

렉시컬 환경 객체는 두 부분으로 구성된다.

1. **환경 레코드(Environment Record) :** 모든 지역 변수를 프로퍼티로 저장하고 있는 객체로 `this` 값과 같은 기타 정보도 여기에 저장된다.
2. **외부 렉시컬 환경(Outer Lexical Environment)에 대한 참조 :** 외부 코드와 연관됨

**"변수**는 내부 특수 환경 객체인 `환경 레코드`의 프로퍼티일 뿐이다. **"변수를 가져오거나 변경"** 하는 것은 **"환경 레코드의 프로퍼티를 가져오거나 변경"**함을 의미한다.

아래 두 줄짜리 코드엔 렉시컬 환경이 하나만 존재한다.

<img src="https://user-images.githubusercontent.com/52126612/91043386-726ff980-e64e-11ea-8e83-6ac7a7d29c3e.png" alt="DAC375F3-A2F6-42B9-AAB4-3913ECE68E89"  />

이렇게 스크립트 전체와 관련된 렉시컬 환경은 **전역 레시컬(global Lexical Environment)**라고 한다.

위 그림에서 네모 상자는 변수가 저장되는 환경 레코드를 나타내고 붉은 화살표는 **외부 참조(outer reference)**를 나타낸다. 전역 레시컬 환경은 외부 참조를 갖지 않기 때문에 화살표가 `null`을 가리킨다.

코드가 실행되고 실행 흐름이 이어져 나가면 렉시컬 환경은 변화한다.

![439F6EB8-7046-4C82-8C9A-B72CF6553604](https://user-images.githubusercontent.com/52126612/91043379-700d9f80-e64e-11ea-8512-877d6afd913a.png)

우측 네모 상자들은 코드가 한 줄, 한 줄 실행될 때마다 전역 렉시컬 환경이 어떻게 변화하는지 보여준다.

1. 스크립트가 실행되면 스크립트 내 선언한 변수 전체가 렉시컬 환경에 올라간다. (pre-populated)
   * 이때 변수의 상태는 특수 내부 상태(special internal state)인 'uninitialized'가 되는데, 자바스크립트 엔진은 'uninitialized' 상태의 변수를 인지하지만, `let`을 만나기 전까지 이 변수를 참조할 수 없다.
2. `let phrase`는 아직 값을 할당하기 전이기 때문에 프로퍼티 값은 `undefined`이다.
3. `phrase`에 갑이 할당되었다.
4. `phrase`의 값이 변경되었다.

요약 :

* 변수는 특수 내부 객체인 환경 레코드의 프로퍼티이다. 환경 레코드는 현재 실행 중인 함수와 코드 블록, 스크립트와 연관되어 있다.
* 변수를 변경하면 환경 레코드의 프로퍼티가 변경된다.

>  **ℹ️ 렉시컬 환경은 명세서에만 존재한다.**
>
> '렉시컬 환경'은 명세서에서 자바스크립트가 어떻게 동작하는지 설명하는 데 쓰이는 '이론상의' 객체로 코드를 사용해 직접 렉시컬 환경을 얻거나 조작하는 것은 불가능하다.
>
> 자바스크립트 엔진들은 명세서에 언급된 사항을 준수하면서 엔진 고유의 방법을 사용해 렉시컬 환경을 최적화하고, 사용하지 않는 변수를 버려 메모리를 절약하는 방법을 사용한다.

<br>

#### 단계 2. 함수 선언문

함수는 변수와 마찬가지로 값이다. 다만, **함수 선언문(function declaration)**으로 선언한 함수는 일반 변수와 달리 바로 **초기화**된다는 점에서 차이가 있다.

함수 선언문으로 선언한 함수는 렉시컬 환경이 만들어지는 즉시 사용할 수 있다. 변수는 `let`을 만나 선언 될 때까지 사용할 수 없지만 말이다. 또한 선언되기 전에도 함수를 사용할 수 있는 것은 바로 이 때문이다.

![0D0431DA-CDED-479C-9793-B4FC6BD45B17](https://user-images.githubusercontent.com/52126612/91043373-6d12af00-e64e-11ea-9d3e-5839a964999f.png)

이런 동작 방식은 함수 선언문으로 정의한 함수에만 적용된다 .`let say = function(name) ...` 과 같이 함수를 변수에 할당한 함수 표현식(function expression)은 해당하지 않는다.

<br>

#### 단계 3. 내부와 외부 렉시컬 환경

함수를 호출해 실행하면 새로운 렉시컬 환경이 자동으로 만들어진다. 이 렉시컬 환경엔 함수 호출 시 넘겨받은 매개변수와 함수의 지역 변수가 저장된다.

`say("John")`을 호출하면 아래와 같은 내부 변화가 일어난다.

![7424E589-9DFA-46FE-A297-6FAFCE90600B](https://user-images.githubusercontent.com/52126612/91043383-71d76300-e64e-11ea-8d09-1226beac5dca.png)

함수가 호출 중인 동안은 호출 중인 함수를 위한 내부 렉시컬 환경과 내부 렉시컬 환경이 가리키는 외부(전역) 렉시컬 환경 두 개를 갖게 된다.

* 예시의 내부 렉시컬 환경은 현재 실행 중인 함수 `say`에 상응한다. 내부 렉시컬 환경엔 함수의 인자인 `name`으로부터 유래한 프로퍼티 하나만 있고, `say("John")`을 호출했기 때문에 `name`의  값은 `"John"`이 된다.
* 예시의 외부 렉시컬 환경은 전역 렉시컬 환경으로 전역 렉시컬 환경은 `phrase`와 `say`를 프로퍼티로 갖는다.

그리고 내부 렉시컬 환경은 **외부** 렉시컬 환경에 대한 참조를 갖는다.

**코드에서 변수에 접근할 땐 먼저 내부 렉시컬 환경을 검색 범위로 잡습니다. 내부 렉시컬 환경에서 원하는 변수를 찾지 못하면 검색 범위를 내부 렉시컬 환경이 참조하는 외부 렉시컬 환경으로 확장하는데, 이러한 과정은 전역 렉시컬 환경으로 확장될 때까지 반복된다.**

전역 렉시컬 환경에 도달할 때까지 변수를 찾지 못하면 엄격 모드에선 에러가 발생하고, 비 엄격 모드에선 정의되지 않는 변수에 값을 할당하려고 하면 에러가 발생하는 대신 새로운 전역 변수가 만들어지는데, 이는 하위 호환성을 위해 남아있는 기능이다.

다시 정리하면 다음과 같다.

* 함수 `say` 내부의 `alert`에서 변수 `name`에 접근할 땐, 먼저 내부 렉시컬 환경을 살펴, 내부 렉시컬 환경에서 변수 `name`을 찾는다.
* `alert` 변수에서 `phrase`에 접근하려는데, 내부 렉시컬 환경에서 `phrase`가 없기 때문에 내부 렉시컬 환경에서 참조 중인 외부 렉시컬 환경으로 확장하여 `phrase`를 찾는다.

<br>

#### 단계 4. 반환 함수

`makeCounter` 예시 :

```javascript
function makeCounter() {
    let count = 0;
    return function() {
        return count++;
    };
}
```

여기에서도 보면 다음과 같은 순서로 `count`를 찾는다.

* `count`가 존재하는 `function()` 내부 렉시컬 환경에서 먼저 `count` 변수를 찾는다.
* 만약 내부 렉시컬 환경인 `function()` 내부에 `count`가 없다면 `function()`이 참조하고 있는 외부 렉시컬 환경인 `makeCounter()` 함수 내의 렉시컬 환경에서 `count`를 찾고, 찾았다면 해당 `count` 변수를 참조하여 진행한다

> **ℹ️ 클로저(closure)**
>
> 클로저는 외부 변수를 기억하고 이 외부 변수에 접근할 수 있는 함수를 의미한다. 몇몇 언어에선 클로저를 구현하는게 불가능하거나 특수한 방식으로 함수를 작성해야 클로저를 만들 수 있다. 하지만 자바스크립트에서는 모든 함수가 자연스럽게 클로저가 된다. 예외가 하나 있긴 하지만 대체로 가능하다.
>
> 요점은 자바스크립트의 함수는 숨김 프로퍼티인 `[[Environment]]`를 이용해 자신이 어디서 만들어졌는지를 기억한다. 함수 내부의 코드는 `[[Environment]]`를 사용해 외부 변수에 접근한다.
>
> 프론트엔드 개발자 채용 인터뷰에서 "클로저가 무엇입니까?"라는 질문을 받는다면, 클로저의 정의를 말하고 자바스크립트에서 왜 모든 함수가 클로저인지에 관해 설명하면 될 것 같다. 이때 `[[Environment]]` 프로퍼티와 렉시컬 환경이 어떤 방식으로 동작하는지에 대한 설명을 덧붙이면 좋다.

<br>

<br>

## 오래된 'var'

> **ℹ️ 오래된 스크립트를 읽는 데 도움을 주는 내용**

변수 선언 방법에는 세 가지가 있는데, 여기서 `var`로 선언한 변수는 `let`으로 선언한 변수와 유사하다. 대부분의 경우에는 `let`을 `var`로, `var`를 `let`으로 바꿔도 큰 문제 없이 동작한다.

```javascript
var message = "안녕하세요.";
alert(message);
```

하지만 `var`는 초기 자바스크립트 구현 방식 때문에 `let`과 `const`로 선언한 변수와는 다른 방식으로 동작한다. 근래엔 `var`를 쓰지 않아 흔치 않지만, 괴물 같은 존재이다. 그렇다면 `var`를 사용 중인 오래된 스크립트를 `let`으로 바꾸려면 어떻게 해야할까?

<br>

### `var`는 블록 스코프가 없다.

`var`로 선언한 변수의 스코프는 함수 스코프이거나 전역 스코프로 블록 기준으로 스코프가 생기지 않기 때문에 블록 밖에서 접근 가능하다.

예시 :

```javascript
if (true) {
    var test = true;
}
alert(test); // true
```

`var`는 코드 블록을 무시하기 때문에 `test`는 전역 변수가 된다. 전역 스코프에서 이 변수에 접근할 수 있다.

반복문에서도 유사한 일이 일어나는데, `var`는 블록이나 루프 수준의 스코프를 형성하지 않기 때문이다.

```javascript
for (var i=0; i<10; i++) {
    // ...
}
alert(i); // 10
```

하지만 코드 블록이 함수 안에 있다면, `var`는 함수 레벨 변수가 된다.

```javascript
function sayHi() {
  if (true) {
    var phrase = "Hello";
  }
  alert(phrase); // "Hello"
}

sayHi();
alert(phrase); // Error: phrase is not defined
```

<br>

### "var" tolerates redeclarations

`let`의 경우 동일한 변수명을 두 번 선언할 수 없다.

```javascript
let user;
let user; // SyntaxError: 'user' has already been declared
```

하지만 `var`의 경우에는 동일한 변수명을 두 번 선언할 수 있고 값 역시 변경된다.

```javascript
var user = "Pete";
var user = "John";
alert(user); // John
```

<br>

### 선언하기 전 사용할 수 있는 `var`

`var` 선언은 함수가 시작될 때 처리된다. 전역에서 선언한 변수라면 스크립트가 시작될 때 처리된다.

함수 본문 내에서 `var`로 선언한 변수는 선언 위치와 상관없이 함수 본문이 시작되는 지점에서 정의된다. (단, 변수가 중첩 함수 내에서 정의되지 않아야 이 규칙이 적용된다.

예시 : `1`과 `2`는 동일하게 작동한다.

```javascript
// 1
function sayHi() {
    phrase = "Hello";
    alert(phrase);
    var phrase;
}
sayHi();
// 2
function sayHi() {
    var phrase;
    phrase = "Hello";
    alert(phrase);
}
```

이렇게 변수가 끌어올려 지는 현상을 **'호이스팅(hoisting)'**이라 부른다. `var`로 선언한 모든 변수는 함수의 최상위로 '끌어 올려지기' 때문이다.

<br>

<br>

## 전역 객체

전역 객체를 사용하면 어디서나 사용 가능한 변수나 함수를 만들 수 있다. 전역 객체는 언어 자체나 호스트 환경에 기본 내장되어 있는 경우가 많은데, 브라우저 환경에선 전역 객체를 `window`, Node.js 환경에서는 `global`이라고 부른다. 각 호스트 환경마다 이름은 다르다.

전역 객체 이름을 `globalThis`로 표준화하자는 내용이 최근에 자바스크립트 명세에 추가되었기 때문에 모든 호스트 환경이 이를 따라야한다. `chromium` 기반이 아닌 몇몇 브라우저는 아직 `globalThis`를 지원하진 않지만 이에 대한 폴리필(polyfill)을 쉽게 만들 수 있다.

튜토리얼은 브라우저 환경에서 구동되기 때문에 `window` 라는 전역 객체를 사용하도록 한다. 만약 다른 호스트 환경에서 작업한다면, `globalThis`를 사용하면 된다.

예시 :

```javascript
alert("Hello");
window.alert("Hello");
```

브라우저에서 `let`이나 `const`가 아닌 `var`로 선언한 전역 함수나 전역 변수는 전역 객체의 프로퍼티가 된다.

```javascript
var gVar = 5;
alert( window.gVar );
```

하위 호환성 때문에 이런 방식으로 전역 객체를 사용해도 동작하지만, 추천하지 않는다. 모듈을 사용하는 모던 자바스크립트는 이런 방식을 지원하지 않는다.

`var` 대신 `let`을 사용하면 위 예시와 달리 전역 객체를 통해 변수에 접근할 수 없다.

```javascript
let gLet = 5;
alert( window.gLet ); // undefined
```

중요한 변수라서 모든 곳에서 사용할 수 있게 하려면, 아래와 같이 전역 객체에 직접 프로퍼티를 추가해줘야한다.

```javascript
window.currentUser = {
    name: "John"
};

alert( currentUser.name );
alert( window.currentUser.name );
```

