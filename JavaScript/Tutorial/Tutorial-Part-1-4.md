# Javascript Tutorial 1 - Objects

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용을 정리한 글이다. 정리할 내용은 다음과 같다.
>
>  * 객체 : Objects
>  * 참조에 의한 객체 복사 : Object copying, references
>  * 가비지 컬렉션 : Garbage collection
>  * 메서드와 "this" : Object methods, "this"
>  * "new" 연산자와 생성자 함수 : Constructor, operator "new"
>  * 옵셔널 체이닝 '?.' : Optional chaining
>  * 심볼형 : Symbol type
>  * 객체를 원시형으로 변환하기 : Object to primitive conversion

<br>

## 객체 (Objects)

자료형 챕터에서 배웠던 여덟 가지 자료형 중 일곱 가지는 오직 하나의 데이터(문자열, 숫자 등)만 담을 수 있어 '원시형 (primitive type)'이라 부른다. 그러나 객체형은 원시형과 달리 다양한 데이터를 담을 수 있습니다. 키로 구분된 데이터 집합이나 복잡한 개체(entity)를 저장할 수 있다. 객체는 자바스크립트 거의 모든 면에 녹아있는 개념으로 자바스크립트를 잘 다루려면 객체를 잘 이해해야한다.

객체는 중괄호 `{...}` 을 이용해 만들 수 있고 중괄호 안에는 `키(key) : 값(value)` 쌍을 구성된 프로퍼티(property) 를 여러 개 넣을 수 있는데, `키`엔 문자형 `값`엔 모든 자료형이 허용된다. 프로퍼티 키는 `프로퍼티 이름`이라고도 부른다.

빈 객체를 만드는 방법 두 가지

```javascript
let user = new Object();
let user = {};
```

중괄호 `{...}` 을 이용해 객체를 선언하는 것을 객체 리터럴(object literal) 이라고 부르고 주로 이 방법을 사용합니다.

<br>

### 리터럴(literal) 과 프로퍼티(property)

```javascript
let user = {
    name: "John",   // 첫 번째 프로퍼티, 키: "name", 값: "John"
    age:30			// 두 번째 프로퍼티, 키: "age", 값: 30
};
```

프로퍼티 값엔 모든 자료형이 올 수 있고, 언제든지 값을 추가하고 삭제 할 수 있다.

```javascript
// add
user.isAdmin = true;
// delete
delete user.age;
```

<br>

### 대괄호 표기법

여러 단어를 조합해 프로퍼티 키를 만든 경우엔, 점 표기법을 사용해 프로퍼티 값을 읽을 수 없습니다.

```javascript
let user = {
    "User Name": "John",
};
alert (user.User Name);// 문법 에러
```

그럴 때를 대비하여 대괄호 표기법을 이용하여 값을 참조할 수 있다.

```javascript
user['User Name'] = "Mike";
```

#### 계산된 프로퍼티

객체를 만들 때 객체 리터럴 안의 프로퍼티 키가 대괄호로 둘러싸여 있는 경우, 이를 계산된 프로퍼티(computed property) 라고 부릅니다.

```javascript
let fruit = prompt("어떤 과일을 구매하시겠습니까?", "apple");
let bag = {
  [fruit]: 5, // 변수 fruit에서 프로퍼티 이름을 동적으로 받아 옵니다.
};

alert( bag.apple ); // fruit에 "apple"이 할당되었다면, 5가 출력됩니다.
```

<br>

### 단축 프로퍼티

실무에서는 프로퍼티 값을 기존 변수에서 받아와 사용하는 경우가 종종 있습니다.

```javascript
function makeUser(name, age) {
    return {
        name: name,
        age,		// age: age
        // ...
    };
}

let user = makeUser("John", 30);
alert(user.name)
```

<br>

### `in` 연산자로 프로퍼티 존재 여부 확인하기

자바스크립트 객체의 중요한 특징 중 하나는 다른 언어와는 달리, 존재하지 않는 프로퍼티에 접근하려 해도 에러가 발생하지 않고 `undefined`를 반환한다는 점이다.

```javascript
// 고전적인 방법
let user = {};
alert (user.name == undefined); // true

// in 연산자
alert ("name" in user); // false
```

<br>

## `for ... in` 반복문

`for ... in` 반복문을 사용하면 객체의 모든 키를 순회할 수 있습니다.

```javascript
for (key in object) {
    // 각 프로퍼티 키(key)를 이용하여 본문(body)를 실행합니다.
}
```

```javascript
let user = {
    name: "John",
    age: 30,
    isAdmin: true
}

for (let key in user) {
    alert (key);
    alert (user[key]);
}
```

#### 객체 정렬 방식

객체와 객체 프로퍼티를 다루다 보면 "프로퍼티엔 순서가 있을까?"라는 의문이 생기기 마련인데, 반복문은 프로퍼티를 추가한 순서대로 실행될지 아니면 순서는 항상 동일할지 궁금해진다. 정답은 "객체는 특별한 방식으로 정렬" 된다는 것인데, 정수 프로퍼티(integer property)는 자동으로 정렬되고, 그 외의 프로퍼티는 객체에 추가한 순서 그대로 정렬됩니다.

<br>

<br>

## 참조에 의한 객체 복사

객체와 원시 타입의 근본적인 차이 중 하나는 객체 '참조에 의해 (by reference)' 저장되고 복사된다는 것이다. 코드를 보고 넘어가자

```javascript
let user = {name: 'John'};
let admin = user;		// admin에 user 복사 - admin에 user 참조값 저장

admin.name = 'Pete';
alert(user.name);		// 'Pete'가 출력된다.
```

객체는 원시값과 다른 동작 방식을 갖고 있는데, 변수엔 객체가 그대로 저장되는 것이 아니라, 객체가 저장되어 있는 '메모리 주소'인 객체에 대한 '참조 값'이 저장된다.

#### 참조에 의한 비교

객체 비교 시 동등 연산자 `==`와 일치 연산자 `===`는 동일하게 동작한다.

```javascript
let a = {};
let b = a;
alert (a == b);	// true
alert (a === b); // true
```

```javascript
let a = {};
let b = {};
alert (a == b); // false - 메모리 참조값이 다르기 때문이다.
```

<br>

### 객체 복사, 병합과 Object.assign

객체가 할당된 변수를 복사하면 동일한 객체에 대한 참조 값이 하나 더 만들어진다. 그렇다면 독립된 객체로 동일한 객체를 복사하고 싶다면 어떻게 해야 할까? 방법은 있지만 자바스크립는 객체 복제 내장 메서드를 지원하지 않기 때문에 조금 복잡하다. 그 이유는 실무에서도 객체를 복제해야 할 일은 거의 없기에 참조에 의한 복사로 해결 가능한 일이 대다수이다. 만약에, 복제가 필요한 상황이라면 새로운 객체를 만든 다음 기존 객체의 프로퍼티를 순회하여 복사하면 된다.

#### 순회하는 방법

```javascript
let user = {
  name: "John",
  age: 30
};

let clone = {}; // 새로운 빈 객체

// 빈 객체에 user 프로퍼티 전부를 복사해 넣습니다.
for (let key in user) {
  clone[key] = user[key];
}

// 이제 clone은 완전히 독립적인 복제본이 되었습니다.
clone.name = "Pete"; // clone의 데이터를 변경합니다.

alert( user.name ); // 기존 객체에는 여전히 John이 있습니다.
```

#### Object.assign 을 사용하는 방법

```javascript
// Object.assign(dest, [src1, src2, src3, ...]);
cloneObject = Object.assign({}, object1, object2, ...)
```

* `dest` 는 목표로 하는 객체
* `src1, ..., srcN` 은 복사하고자 하는 객체
* 객체 `src1, ..., srcN` 의 프로퍼티를 `dest`에 복사한다. `dest`를 제외한 인수(객체)의 프로퍼티 전부가 객체로 복사한다.
* `dest` 반환

```javascript
let user = { name: "John" };

let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

// permissions1과 permissions2의 프로퍼티를 user로 복사합니다.
Object.assign(user, permissions1, permissions2);

// now user = { name: "John", canView: true, canEdit: true }
```

<br>

<br>

## 가바지 컬렉션

자바스크립트는 눈에 보이지 않는 곳에서 메모리 관리를 수행한다. 우리가 만드는 모든 것들 원시값, 객체, 함수 등은 메모리를 차지하는데, 그렇다면 더는 쓸모 없어지게 된 것들은 어떻게 처리될까?

<br>

### 가비지 컬렉션 기준

자바스크립트는 도달 가능성(reachability) 라는 개념을 사용해 메모리 관리를 수행하는데, 도달 가능한 값이란 쉽게 말해 어떻게든 접근하거나 사용할 수 잇는 값을 의미한다. 그렇기에 도달 가능한 값은 메모리에서 삭제되지 않는다.

1. 루트 (root)
    * 현재 함수의 지역 변수와 매개변수
    * 중첩 함수의 체인에 있는 함수에서 사용되는 변수와 매개변수
    * 전역 변수
    * 기타 등등

2. 루트가 참조하는 값이나 체이닝으로 루트에서 참조할 수 있는 값

이렇듯 자바스크립트 엔진 내에서는 가비지 컬렉터가 끊임없이 동작해 모든 객체를 모니터링하고, 도달할 수 없는 객체는 삭제한다.

<br>

#### 간단한 예시

```javascript
// 객체 user 생성
let user = {
    name: "John"
};
// user 덮어쓰기
user = null;
// 이렇게 된다면, 더 이상 user.name은 참조할 수 없기 때문에
// 도달할 수 없는 상태가 되고 John을 메모리에서 삭제한다.
```

<br>

<br>

## 메서드와 "this"

객체는 사용자(user), 주문(order) 등과 같이 실제 존재하는 객체를 표현하고 할 때 생성하는데 이 때 메서드란, 객체의 프로퍼티에 함수를 할당해 객체에게 행동할 수 있는 능력을 부여하는 것이다.

<br>

### 메서드 만들기

```javascript
let user = {
    name: "John",
    age: 30
};

user.sayHi = function() {
    alert('안녕하세요!');
};

user.sayHi(); // 안녕하세요!
```

이런 방식으로 이전의 `user.isAdmin`이라는 값을 추가할 때처럼 함수를 할당해줄 수 있고, 사용할 때 역시 함수와 동일하게 `()`로 이용할 수 있다.

#### 메서드 단축 구문

```javascript
let user = {
    sayHi() {
        alert("안녕하세요!");
    };
};
```

<br>

### 메서드와 "this"

메서드는 객체에 저장된 정보에 접근할 수 있어야 제 역할을 할 수 있는데, 쉽게 말하면 객체에 아무리 메서드를 할당할 수 있더라도 저장된 정보에 접근할 수 없다면 결국에는 분리하여 함수를 만드는게 더 낫기 때문이다. 그렇기에 객체 내부의 메서드는 객체에 저장된 정보에 접근할 수 있고, 활용한다. (모든 메서드가 그렇다는 것은 아니다.)

접근 방법은 간단하다. `this` 키워드를 이용하면 객체에 접근할 수 있다.

```javascript
let user = {
    name: "John",
    age: 30,
    
    sayHi() {
        alert(`안녕하세요! ${this.name} 입니다.`);
    }
};

user.sayHi();
```

<br>

### 자유로운 "this"

자바스크립트의 `this`는 다른 프로그래밍 언어의 `this`와 동작 방식이 다른데, 자바스크립트에서는 모든 함수에 `this`를 사용할 수 있기 때문이다.

```javascript
function sayHi() {
    alert( this.name );
}
```

```javascript
let user = { name: "John" };
let admin = { name: "Admin" };

// 별개의 객체에서 동일한 함수를 사용함
user.f = sayHi;
admin.f = sayHi;

// this 값이 달라짐
user.f(); // John  (this == user)
admin.f(); // Admin  (this == admin)

sayHi(); // 아무것도 나타나지 않음
```

이렇듯 어떤 함수에서는 `this`를 사용할 수 있고 만약 참조할 객체가 없다면 아무런 값도 리턴하지 않는다.

<br>

<br>

## "new" 연산자와 생성자 함수

객체 리터럴인 `{...}` 을 사용하면 객체를 쉽게 만들 수 있다. 그런데 개발을 하다 보면 유사한 객체를 여러 개 만들어야할 때가 생기곤 하는데, 복수의 사용자, 메뉴 내 다양한 아이템을 객체로 표현하려고 하는 경우가 그 예시이다. 이런 상황에서는 `"new"` 연산자와 생성자 함수를 사용하면 유사한 객체를 여러 개 쉽게 만들 수 있다.

<br>

### 생성자 함수

생성자 함수(constructor function)와 일반 함수에 기술적인 차이는 없으나, 생성자 함수는 아래 두 관례를 따른다.

1. 함수 이름의 첫 글자는 대문자로 시작한다.
2. **<u>반드시</u>** `new` 연산자를 붙여 실행한다.

```javascript
function User(name) {
    this.name = name;
    this.isAdmin = false;
}

let user = new User("Jack")

console.log(user.name);		// Jack
console.log(user.isAdmin);	// false
```

동작 방식은 다음과 같다.

1. <u>빈 객체</u>를 만들어 `this`에 할당
2. 함수 본문을 실행하여, `this`에 새로운 프로퍼티를 추가하여 `this`를 수정
3. `this`를 반환 (`this` : 객체)

주의) 모든 함수는 생성자 함수가 될 수 있어 `new`를 붙여 실행한다면 어떠한 함수라도 위에 언급된 알고리즘이 실행된다.

> ℹ️ new function() {...}
>
> 재사용할 필요가 엉ㅂㅅ는 복잡한 객체를 만들어야 한다고 가정하자. 많은 양의 코드가 필요할 것이고, 이럴 때엔 아래와 같이 코드를 익명 생성자 함수를 감싸주는 방식을 사용할 수 있다.
>
> ```javascript
> let user = new function() {
>     this.name = "John";
>     this.isAdmin = "false";
> };
> ```
>
> `function()`는 익명 함수이기 때문에 어디에도 저장되지 않아, 다른 곳에서 동일한 함수를 작성하지 않는 한 재사용할 수 없다.

<br>

<br>

## 옵셔널 체이닝 '?.'

> ⚠️ **최근에 추가**되어 구식 브라우저는 폴리필이 필요하다.

옵셔널 체이닝 (optional chaining) `?.`을 사용하면 프로퍼티가 없는 중첩 객체에도 에러 없이 안전하게 접근할 수 있습니다.

<br>

### 옵셔널 체이닝이 필요한 이유

옵셔널 체이닝이 등장하게 된 사례 예시, 사용자가 여러 명이 있는데, 그 중 몇 명은 주소 정보를 가지고 있지 않다고 가정한다. 이럴 때 `user.address.street`를 사용하여 주소에 접근하면 에러가 발생할 수 있다.

```javascript
let user = {}; // 주소정보가 없는 사용자
alert(user.address.street); // TypeError: Cannot read property ...
```

또 다른 사레는 브라우저에서 동작하는 코드를 개발하는 도중에 발생할 수 있는 문제로 페이지에 있는 특정 요소에 담긴 정보에 접근하려는데, 요소가 페이지에 없는 경우이다.

```javascript
// querySelector(...) 호출 결과가 null인 경우
let html = document.querySelector('.my-element').innerHTML; // TypeError: ...
```

> 둘 다 TypeError 네.. 참조할 수 없을 때에는 TypeError가 나는구나!

이런 상황에서 명세서에 `?.`이 추가되기 전엔 `&&` 연산자를 사용하곤 했습니다. 하지만 이 방법은 코드가 아주 길어진다는 문제가 생긴다.

```javascript
let user = {}; // 주소정보가 없는 사용자
alert( user && user.address && user.address.street ); // undefined
```

<br>

### 옵셔널 체이닝의 등장

`?.`은 `?.`"앞"의 평가 대상이 `undefined`나 `null` 이면 평가를 멈추고 `undefined`를 반환한다.

```javascript
let user = {};
alert( user?.address?.street ); // undefined
```

```javascript
let user = null;

alert( user?.address ); // undefined
alert( user?.address.street ); // undefined
alert( user?.address.street.anything ); // undefined
```

> ⚠️ 옵셔널 체이닝을 남용하지 말자.
>
> `?.` 는 존재하지 않아도 괜찮은 대상에만 사용해야하는데, 무차별적으로 사용하게 되면 있어야하는 대상을 찾지 못하는 경우가 생길 수 있다.

<br>

<br>

## 심볼형

자바스크립트는 객체 프로퍼티 키로 오직 문자형과 심볼형만을 허용한다. 지금까지는 프로퍼티 키가 문자형인 경우에만 살펴보았는데, 심볼형 키를 사용할 때의 이점에 대해 살펴본다.

### 심볼

'심볼(symbol)'은 유일한 식별자(unique identifier)를 만들고 싶을 때 사용한다.

```javascript
let id = Symbol('id'); // 심볼 이름을 설정할 수 있다.
```

<br>

<br>

## 객체를 원시형으로 변환하기

`obj1 + obj2` 와 같이 객체끼리 더하는 연산을 하거나, 빼는 연산을 한다면 객체는 <u>자동 형 변환</u>이 일어난다. 객체는 원시값으로 변환되고, 그 후 의도한 연산이 수행된다.

1. 객체는 논리 평가 시 `true`를 반환합니다. 따라서 객체는 숫자나 문자형으로만 형변환이 일어난다.
2. 숫자형으로 형 변환은 객체끼리 수학 관련 연산을 적용할 때 일어나고, 만약 객체 `Date` 끼리 차감하면 두 날짜의 시간 차이가 반환한다.
3. 문자형으로의 형 변환은 대게 `alert(obj)` 와 같이 객체를 출력하력 할 때 일어난다.

<br>

### ToPrimitive

특수 객체 메서드를 사용하면 숫자형이나 문자형으로의 형 변환을 원하는 대로 조절할 수 있습니다. 객체 형 변환은 세 종류로 구분되는데, `hint`라 불리는 값이 구분 기준이 됩니다.

#### String

```javascript
// 객체를 출력
alert(obj);
// 객체를 프로퍼티 키로 사용하고 있는 경우
anotherObj[obj] = 123;
```

#### number

수학 연산을 적용하려 할 때 (객체-숫자형 변환), hint는 number가 된다.

```javascript
// 명시적 형 변환
let num = Number(obj);
// (이항 덧셈 연산을 제외한) 수학 연산
let n = +obj; // 단항 덧셈 연산
let delta = date1 - date2;
// 크고 작음 비교하기
let greater = user1 > user2;
```

#### default

연산자가 기대하는 자료형이 "확실치 않을 때" hint는 `default`가 됩니다. 아주 드물게 발생

이항 덧셈 연산자 `+` 는 피연산자의 자료형에 따라 문자열을 합치는 연산을 할 수도 있고 숫자를 더해주는 연산을 할 수도 있는데, 그러다보니 `+` 인수가 객체일 때에는 `default`가 된다.

동등 연산자 `==`를 사용해 객체-문자형, 객체-숫자형, 객체-심볼형끼리 비교할 때도, 객체를 어떤 자료형으로 바꿔야 할지 확신이 안 서므로 hint는 default가 됩니다.

```javascript
// 이항 덧셈 연산은 hint로 `default`를 사용합니다.
let total = obj1 + obj2;
// obj == number 연산은 hint로 `default`를 사용합니다.
if (user == 1) { ... };
```

#### boolean

boolean은 `hint`가 없다 그 이유는 모든 객체는 그저 `true` 이기 때문이다.

<br>

자바스크립트는 형 변환이 필요한 경우, 아래와 같은 알고리즘에 따라 메서드를 찾고 호출한다.

1. 객체에 `obj[Symbol.toPrimitive](hint)` 메서드가 있는지 찾고 있다면 메서드를 호출하는데, `Symbol.toPrimitive`는 시스템 심볼로, 심볼형 키로 사용된다.
2. 1에 해당하지 않고, `hint`가 `string` 이라면,
    * `obj.toString()`이나 `obj.valueOf()`를 호출한다. (존재하는 메서드만 실행)
3. 1, 2에 해당하지 않고, `number`나 `default`라면,
    * `obj.valueOf()`나 `obj.toString()` 을 호출한다. (존재하는 메서드만 실행)

