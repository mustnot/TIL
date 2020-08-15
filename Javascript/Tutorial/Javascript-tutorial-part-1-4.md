# Javascript Tutorial 1 - Objects

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용을 정리한 글이다. 정리할 내용은 다음과 같다.
>
>  * Objects
>  * Object copying, references

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

