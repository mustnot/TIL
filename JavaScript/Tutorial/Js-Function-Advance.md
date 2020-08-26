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

<br>

<br>

## 객체로서의 함수와 기명 함수 표현식

자바스크립트에서 함수는 값으로 취급된다. 또한 모든 값은 자료형을 가지고 있는데, 그럼 함수의 자료형은 무엇일까? **함수의 자료형은 객체**로 함수는 호출 가능한(callable) '행동 객체'로 이해하면 쉽다. 자바스크립트에서 함수는 호출 할 수 있을 뿐만 아니라 객체처럼 함수에 프로퍼티를 추가, 제거하거나 참조를 통해 전달할 수도 있다.

<br>

### `name` 프로퍼티

함수 객체엔 몇 가지 쓸만한 프로퍼티가 있는데, `name` 프로퍼티를 사용하면 함수 이름을 가져올 수 있다.

```javascript
function sayHi() {
    alert("Hi!");
}
alert(sayHi.name); // sayHi
```

함수 객체에 이름을 할당해주는 로직은 아주 똑똑해서 익명 함수라도 자동으로 이름이 할당된다.

이러한 이름을 명세서에서 정의되기를 'contextual name'이라고 부른다. 이름이 없는 함수의 이름을 지정할 땐 컨텍스트에서 이름을 가져온다. 객체 메서드의 이름도 `name` 프로퍼티를 이용해 가져올 수 있다.

```javascript
let user = {
    sayHi() {
        // ...
    }
}
console.log(user.sayHi.name);
```

하지만, 객체 메서드 이름은 함수처럼 <u>자동 할당</u>되지 않는다. 적절한 이름을 추론하는게 불가능한 상황이 있는데, 이런 경우에는 `name` 프로퍼티엔 빈 문자열이 저장된다.

```javascript
let arr = [function() {}];
alert(arr[0].name); // <빈 문자열>
```

<br>

### `length` 프로퍼티

내장 프로퍼티 `length`는 함수 매개변수의 개수를 반환한다. (단, 나머지 매개변수는 개수에 포함되지 않는다.) 

```javascript
function f1(a) {}
function f2(a, b) {}
function many(a, b, ...more) {}

alert(f1.length); // 1
alert(f2.length); // 2
alert(many.length); // 2
```

`length` 프로퍼티는 다른 함수 안에서 동작하는 함수의 [타입을 검사(type introspection)](https://en.wikipedia.org/wiki/Type_introspection) 할 때도 종종 사용된다. 질문에 쓰일 `question`과 질문에 대한 답에 따라 호출할 임의의 수를 `handler` 함수를 함께 받는 함수 `ask`를 예시로 들면, 사용자가 답을 제출하면 `ask` 핸들러 함수를 호출한다. 이때 우리는 두 종류의 핸들러 함수를 `ask`에 전달할 수 있다.

* 인수가 없는 함수로, 사용자가 OK를 클릭했을 때만 호출됨
* 인수가 있는 함수로, 사용자가 OK를 클릭하든 Cancel을 클릭하든 호출됨

그리고 `handler.length` 프로퍼티를 사용하면 상황에 맞는 `handler`를 호출할 수 있다.

```javascript
function ask(question, ...handlers) {
  let isYes = confirm(question);

  for(let handler of handlers) {
    if (handler.length == 0) {
      if (isYes) handler();
    } else {
      handler(isYes);
    }
  }

}

// 사용자가 OK를 클릭한 경우, 핸들러 두 개를 모두 호출함
// 사용자가 Cancel을 클릭한 경우, 두 번째 핸들러만 호출함
ask("질문 있으신가요?", () => alert('OK를 선택하셨습니다.'), result => alert(result));
```

인수의 종류에 따라 인수를 다르게 처리하는 방식을 프로그래밍 언어에선 [다형성(polymorphism)](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) 이라 부른다.

<br>

### 커스텀 프로퍼티

함수에 자체적으로 만든 프로퍼티를 추가할 수 있습니다.

예시 :

```javascript
function sayHi() {
  alert("Hi");
  
  sayHi.counter++;
}
sayHi.counter = 0; // 초기값 설정

sayHi();
sayHi();

console.log(sayHi.counter);
// 나중에 이런 식으로 동일 함수를 n번 실행 시 막는 방식에 사용될 수 있을 것 같다.
```

> **⚠️ 프로퍼티는 변수가 아니다.**
>
> `sayHi.counter = 0`과 같이 함수에 프로퍼티를 할당해도 함수 내 지역변수 `counter`가 만들어지지 않는다. `counter` 프로퍼티와 변수 `let counter`는 전혀 관계가 없다.
>
> 프로퍼티를 저장하는 것처럼 함수를 객체와 같이 다룰 수 있지만, 이는 실행에 아무 영향을 끼치지 않는다. 변수는 함수 프로퍼티가 아니고 함수 프로퍼티는 변수가 아니기 때문에 둘 사이에는 공통점이 없다.

클로저는 함수 프로퍼티로 대체할 수 있다. 앞서 살펴본 바 있는 `counter` 함수를 함수 프로퍼티를 사용해 바꿔보면 다음과 같다.

```javascript
function makeCounter() {
  // let count = 0 대신 아래 메서드(프로퍼티)를 사용함
  function counter() {
    return counter.count++;
  };

  counter.count = 0;
  return counter;
}
let counter = makeCounter();

alert( counter() ); // 0
alert( counter() ); // 1
```

함수 안에 `counter` 함수를 만들어 `count` 프로퍼티가 `makeCounter()` 함수 안에서 존재하도록 했다고 보인다. 그렇다면 이 방법이 클로저를 사용하는 것보다 나은 방법일까?

두 방법의 차이점은 `count` 값이 외부 변수에 저장되어 있는 경우 드러난다. 클로저를 사용한 경우 외부 코드에서 `count`에 접근할 수 없어 오직 중첩함수 내에서만 `count` 값을 수정할 수 있다. 반면 함수 프로퍼티를 사용해 `count` 함수에 바인딩시킨 경우엔 다음 예시와 같이 외부에서 값을 수정할 수 있다.

```javascript
function makeCounter() {
  function counter() {
    return counter.count++;
  };

  counter.count = 0;
  return counter;
}
let counter = makeCounter();

counter.count = 10;
alert( counter() ); // 10
```

<br>

<br>

## `new Function` 문법

함수 표현식과 함수 선언문 외 함수를 만드는 방법이 하나 더 있는데, 잘 사용되지 않는 방법이지만 대안이 없을 때 사용된다.

### 문법

```javascript
let func = new Function ( [arg1, arg2, ..., argN], functionBody );
```

예시 :

```javascript
let sum = new Function ( 'a', 'b', 'return a + b' );
```

```javascript
let sayHi = new Function ( 'alert("Hi!")' );
```

<br>

### 클로저

```javascript
function getFunc() {
  let value = "test";
  let func = new Function('alert(value)');
  return func();
}
getFunc(); // ReferenceError: value is not defined
```

이런 식으로 `new Function`으로 함수를 만들면 함수가 참조하고 있는 외부 렉시컬 환경의 `value` 변수를 참조할 수 없다. 아래와 같이 일반적인 함수 할당으로 사용 가능하다.

```javascript
function getFunc() {
  let value = 'test';
  let func = function() { alert(value) };
  return func();
}
getFunc();
```

<br>

<br>

## setTimeout과 setInterval을 이용한 호출 스케줄링

개발을 하다보면 일정 시간이 지난 후에 원하는 함수를 예약 실행(호출)할 수 있게 하고 싶은데, 이 때 이용하는 것이 '호출 스케줄링(scheduling a call)'이다. 다음 두 가지 방법이 있다.

* `setTimeout`을 이용해 일정 시간이 지난 후에 함수를 실행하는 방법
* `setInterval`을 이용해 일정 시간 간격을 두고 함수를 실행하는 방법

자바스크립트 명세서엔 `setTimeout`과 `setInterval`이 명시되어 있지 않지만 시중에 나와있는 모든 브라우저, Node.js를 포함한 자바스크립트 호스트 환경 대부분이 이와 유사한 메서드와 내부 스케줄러를 지원한다.

<br>

### setTimeout

문법 :

```javascript
let timerId = setTimeout(func|code, [delay], [arg1], [arg2], ...)
```

* `func|code` : 실행하고자 하는 코드로, 함수 또는 문자열 형태이다. 대개는 이 자리에 함수가 들어가며, 하위 호환성을 위해 문자열도 받을 수 있게 해놓았지만, 추천하진 않는다.
* `delay` : 실행 전 대기 시간으로, 단위는 밀리초(milisecond, 1000ms = 1s)로 기본값은 0이다.
* `arg1, arg2, ...` : 함수에 전달할 인수들로 IE9 이하에선 지원하지 않는다.

예시 :

```javascript
function sayHi() {
  alert('Hi!');
}
setTimeout(sayHi, 1000); // 1초 뒤 실행

function sayHi2(who, phrase) {
  alert( `Hi! ${who} ${phrase}` );
}
setTimeout(sayHi2, 1000, "John", "GoodMorning!") //

// 익명 화살표 함수를 이용한 방법
setTimeout(() => alert('안녕하세요.'), 1000);
```

#### clearTimeout으로 스케줄링 취소

`setTimeout`을 호출하면 '타이머 식별자(timer identifier)'가 반환되는데, 스케줄링을 취소하고 싶을 땐 이 식별자(`timerId`)를 사용하면 된다.

예시 :

```javascript
let timerId = setTimeout(() => alert('안녕하세요.'), 10000);
clearTimeout(timerId)
```

<br>

###  setInterval

`setInterval` 메서드는 `setTimeout`과 동일한 문법을 사용한다.

```javascript
let timerId = setTimeout(func|code, [delay], [arg1], [arg2], ...)
```

예시 :

```javascript
let timerId = setInterval(() => alert("tictoc"), 2000);
setTImeout(() => { clearInterval(timerId); alert('stop'); }, 5000);
```

<br>

### 중첩 setTimeout

무언가를 일정 간격을 두고 실행하는 방법에는 크게 2가지가 있는데, 하나는 `setInterval`을 이용하는 방법이고, 다른 하나는 아래 예시와 같이 중첩 `setTimeout`을 이용하는 방법이다.

```javascript
let timerId = setTimeout(function tick() {
  alert("tick");
  timerId = setTimeout(tick, 2000);
}, 2000);
```

<br>

### 대기시간이 0인 setTimeout

`setTimeout(func, 0)` 혹은 `setTimeout(func)`를 사용하면 `setTimeout`의 대기 시간을 0으로 설정할 수 있다. 이렇게 대기 시간을 `0`으로 설정하면 `func`를 '가능한 한' 빨리 실행할 수 있다. 다만, 현재 스케줄링인 스크립트의 처리가 종료된 이후에 스케줄링 함수가 실행된다.

이런 특징을 이용해 현재 스크립트의 실행이 종료된 '직후에' 원하는 함수가 실행될 수 있게 할 수 있다.

예시 :

```javascript
setTimeout(() => console.log("World"));
console.log("Hello");
```

<br>

<br>

## call/apply와 데코레이터, 포워딩

자바스크립트는 함수를 다룰 때 탁월한 유연성을 제공한다. 함수는 이곳저곳 전달될 수 있고, 객체로도 사용될 수 있다. 이번 챕터에선 함수 간에 호출을 어떻게 `포워딩(forwarding)` 하는지, 함수를 어떻게 `데코레이팅(decorating)` 하는지 살펴본다.

<br>

### 코드 변경 없이 캐싱 기능 추가하기

CPU를 많이 잡아먹지만 결과는 안정적인 함수 `slow(x)`가 있다고 가정할 때, 결과가 안정적이라는 말은 `x`가 같으면 호출 결과도 같다는 것을 의미한다. 그리고 만약 `slow(x)`가 자주 호출된다면, 결과를 어딘가에 저장(캐싱)해 재연산에 걸리는 시간을 줄이고 싶을 것이다.

아래 예시는 `slow()` 안에 캐싱 관련 코드를 추가하는 대신, 래퍼 함수를 만들어 캐싱 기능을 추가할 것이고, 이런 식으로 래퍼 함수를 만들면 여러 가지 이점이 있다.

```javascript
function slow(x) {
  alert(`slow(${x})을/를 호출`);
  return x;
}

function cachingDecorator(func) {
  let cache = new Map();
  
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
    
    let result = func(x);
    
    cache.set(x, result);
    return result;
  };
}

slow = cachingDecorator(slow);

alert( slow(1) ); // slow(1)이 저장되었습니다.
alert( "다시 호출: " + slow(1) ); // 동일한 결과

alert( slow(2) ); // slow(2)가 저장되었습니다.
alert( "다시 호출: " + slow(2) ); // 윗줄과 동일한 결과
```

`cachingDecorator` 같이 인수로 받은 함수의 행동을 변경시켜주는 함수를 데코레이터(decorator)라 부른다. 모든 함수를 대상으로 `cachingDecorator`를 호출 할 수 있는데, 이 때 반환되는 것은 캐싱 래퍼이다. 함수에 `cachingDecorator`를 적용하기만 하면 캐싱이 가능한 함수를 원하는 만큼 구현할 수 있기 때문에 데코레이터 함수는 아주 유용하게 사용된다.

캐싱 관련 코드를 함수 코드와 분리할 수 있기 때문에 함수의 코드가 간결해진다는 장점도 있다.

<br>

### `func.call`을 이용해 컨텍스트 지정하기

위에서 구현한 캐싱 데코레이터는 객체 메서드에 사용하기엔 적합하지 않다. 객체 메서드 `worker.slow()`는 데코레이터 적용 후 제대로 동작하지 않는다.

```javascript
// worker.slow에 캐싱 기능을 추가해봅시다.
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    // CPU 집약적인 작업이라 가정
    alert(`slow(${x})을/를 호출함`);
    return x * this.someMethod(); // (*)
  }
};

// 이전과 동일한 코드
function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
    let result = func(x); // (**)
    cache.set(x, result);
    return result;
  };
}

alert( worker.slow(1) ); // 기존 메서드는 잘 동작합니다.

worker.slow = cachingDecorator(worker.slow); // 캐싱 데코레이터 적용

alert( worker.slow(2) ); // Error: Cannot read property 'someMethod' of undefined
```

`(*)`으로 표시한 줄에 `this.someMethod` 접근에 실패했기 때문에 에러가 발생했는데, 원인은 `(**)` 표시한 줄에 래퍼가 기존 함수 `func(x)`를 호출하면 `this`가 `undefined`가 되기 때문이다. 말 그대로 `this.someMethod()`에서 `this`가 사라지게 된다.

아래 코드는 비슷한 현상을 나타낸다.

```javascript
let func = worker.slow;
func(2);
```

래퍼가 기존 메서드 호출 결과를 전달하려 했지만, `this`의 컨텍스트가 사라지면서 `this.someMethod`를 더 이상 실행할 수 없게 되었다. 이를 해결하기 위해 먼저, `this`를 명시적으로 고정해 함수를 호출할 수 있게 해주는 특별한 내장 함수 메서드 [func.call(context, …args)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function/call)에 대해 알아보자.

문법 :

```javascript
func.call(context, arg1, arg2, ...)
```

메서드를 호출하면 메서드의 첫 번째 인수가 `this`, 이어지는 인수가 `func`의 인수가 된 후 `func`를 호출한다. (음 이게 무슨 소리지..)

예시 :

```javascript
func(1, 2, 3);
func.call(obj, 1, 2, 3);
```

```javascript
function sayHi() {
  alert(this.name);
};

let user = { name: "John" };
sayHi.call( user ); // this = "John"
```

```javascript
function say(phrase) {
  alert( `${this.name}: ${phrase}`);
}
let user = { name: "John" };
say.call( user, "Hello!");
```

그럼 캐싱을 다시 해보면 아래 코드처럼 변경하면 된다.

```javascript
// worker.slow에 캐싱 기능을 추가해봅시다.
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    alert(`slow(${x})을/를 호출함`);
    return x * this.someMethod();
  }
};

function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
    let result = func.call(this, x); // (*) 수정
    cache.set(x, result);
    return result;
  };
}

worker.slow = cachingDecorator(worker.slow); // 캐싱 데코레이터 적용
alert( worker.slow(2) );
```

1. 데코레이터 적용한 후에 `worker.slow`는 래퍼 `function (x) {...}`가 된다.
2. `worker.slow(2)`를 실행하면 래퍼는 `2`를 인수로 받고, `this=worker`가 된다.
3. 결과가 캐시되지 않은 상황이라면, `func.call(this, x)`에서 현재 `this=(worker)`와 인수(`2`)를 원본 메서드에 전달한다.

<br>

### 여러 인수 전달하기

`cachingDecorator`를 다채롭게 해보면, 현재 구조상 하나의 변수만 받을 수 있는데, 만약 복수 인수를 가진 메서드가 있다면 어떻게 해야할까?

```javascript
let worker = {
  slow(min, max) {
    return min + max;
  }
};

worker.slow = cachingDecorator(worker.slow)
```

지금까지는 인수 `x` 하나뿐이었기 때문에, `cache.set(x, result)`로 결과를 저장하고 `cache.get(x)`로 저장된 결과를 불러오기만 하면 되었는데, 이제부터는 `(min, max)`와 같이 인수가 여러 개이고, 이 인수들을 넘겨 호출된 결과를 기억해야한다. 반면에 네이티브 맵은 단일 키만 받는다.

해결 방법은 여러 가지다.

1. 복수 키를 지원하는 맵과 유사한 자료 구조 구현하기 (서드 파티 라이브러리 등을 이용해도 됨)
2. 중첩 맵을 사용하기. `(max, result)` 쌍 저장은 `cache.set(min)`으로 `result`는 `cache.get(min).get(max)`을 사용해 얻는다. 하지만 키가 많아질 수록 복잡해진다.
3. 두 값을 하나로 합치기. `맵`의 키로 `"min, max"`와 같이 하나로 사용하여 키로 이용. 또는 여러 값을 하나로 합치는 해싱 함수를 구현해 이용

```javascript
let worker = {
  slow(min, max) {
    alert(`slow(${min},${max})을/를 호출함`);
    return min + max;
  }
};

function cachingDecorator(func, hash) {
  let cache = new Map();
  return function() {
    let key = hash(arguments); // (*)
    if (cache.has(key)) {
      return cache.get(key);
    }

    let result = func.call(this, ...arguments); // (**)

    cache.set(key, result);
    return result;
  };
}

function hash(args) {
  return args[0] + ',' + args[1];
}

worker.slow = cachingDecorator(worker.slow, hash);

alert( worker.slow(3, 5) ); // 제대로 동작합니다.
alert( "다시 호출: " + worker.slow(3, 5) ); // 동일한 결과 출력(캐시된 결과)
```

<br>

### func.apply

위 같이 여러 인수를 사용할 때에는 `func.call` 대신에 `func.apply`를 사용해도 된다. 

문법 :

```javascript
func.apply(context, args)
```

`apply`는 `func`의 `this`를 `context`로 고정해주고, 유사 배열 객체인 `args`를 인수로 사용할 수 있게 해준다. 

```javascript
func.call(context, args1, args2, ...)
func.apply(context, [arg1, arg2, ...])
```

약간의 차이가 있다.

* 전개 문법 `...`은 이터러블 `args`를 분해하여 `call`에 전달할 수 있게 해준다.
* `apply`는 오직 유사 배열 형태의 `args`만 받는다.

이 차이만 빼면 두 메서드는 완전히 동일하게 동작하는데, 인수가 이터러블 형태라면 `call`을 유사 배열 형태라면 `apply`를 사용하면 된다.

배열같이 이터러블이면서 유사 배열인 객체엔 둘 다를 사용할 수 있는데, 대부분의 자바스크립트 엔진은 내부에서 `apply`를 최적화하기 때문에 `apply`를 사용하는 게 좀 더 빠르긴 하다.

이렇게 컨텍스트와 함께 인수 전체를 다른 함수에 전달하는 것을 콜 포워딩이라 한다.

간단한 형태의 콜 포워딩은 다음과 같다.

```javascript
let wrapper = function() {
  return func.apply(this, arguments);
}
```

