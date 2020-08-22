# Javascript Data Types - Json

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용으로 Part 1의 Data Types을 정리한 글로 그 중 Json 객체에 대해 작성한 글입니다.

<br>

## Json과 메서드

복잡한 객체를 다루고 있다고 가정할 때, 네트워크를 통해 객체를 어딘가에 보내거나 혹은 로깅 목적으로 객체를 출력해야 한다면 객체를 문자열로 전환해야할 필요가 있다. 그리고 이때 전환된 문자열엔 원하는 정보가 있는 객체 프로퍼티 모두가 포함되어야만 한다.

```javascript
let user = {
    name: "John",
    age: 30
    
    toString() {
        return `{name: "$(this.name)", age: $(this.age)}`;
    }
};

alert(user);	// {name: "John", age: 30}
```

그런데 개발을 하다보면 프로퍼티가 추가되거나 삭제, 수정될 수 있다. 그렇게 된다면 위에서 구현한 `toString`을 매번 수정해야 하는 번거롭고 고통스러운 작업이 될 것이다. 그렇다고 프로퍼티에 반복문을 돌리는 방법을 대안으로 사용할 수 있는데, 중첩 객체 등으로 인해 객체가 복잡한 경우 이를 문자열로 변경하는 건 매우 까다로운 작업이라 이 마저도 쉽지 않다. 다행히도 자바스크립트에는 해결할 수 있는 방법으로 이미 구현되어 있는 메서드를 활용하는 방법이다. 

<br>

### JSON.stringify

`JSON (Javascript Object Notation)` 은 값이나 객체를 나타내주는 범용 포맷으로, RFC 4627 표준에 정의되어 있다. JSON은 본래 자바스크립트에서 사용할 목적으로 만들어진 포맷이지만 라이브러리를 사용하면 자바스크립트가 아닌 언어에서도 JSON을 충분히 다룰 수 있어서, JSON을 데이터 교환 목적으로 사용하는 경우가 많습니다. 특히 클라이언트 측 언어가 자바스크립트일 때를 말한다.

자바스크립트 제공하는 JSON 관련 메서드는 아래와 같다.

* `JSON.stringify` : `객체`를 `JSON`으로 바꿔준다.
* `JSON.parse` : `JSON`을 `객체`로 바꿔준다.

예시 :

```javascript
let student = {
    name: "John",
    age: 30,
    isAdmin: false,
    course: ['html', 'css', 'js'],
    wife: null,
};

let json = JSON.stringify(student);

alert(typeof json); // string
```

`JSON.stringify(student)`를 호출하자 `student`가 문자열로 바뀌었는데, 이렇게 변경된 문자열은 JSON으로 인코딩된(JSON-encoded), <u>직렬화 처리된(serialized)</u>, 문자열로 변환된(stringified), 결집된(marshalled) 객체라고 부른다. 객체는 이렇게 문자열로 변환된 후에야 비로소 네트워크를 통해 전송되거나 저장소에 저장할 수 있다.

JSON으로 인코딩된 객체는 일반 객체와 다른 특징을 보인다.

* 문자열은 큰따옴표(")로 감싸야한다. JSON에서는 작은 따옴표(')나 백틱을 사용할 수 없다.
* 객체 프로퍼티 이름을 큰 따옴표로 감싸야한다.

적용 가능한 자료형은 다음과 같다.

* 객체 `{...}`
* 배열 `[...]`
* 원시형 :
    * 문자형
    * 숫자형
    * 불린형 값 `true` or `false`
    * `null`

예시 :

```javascript
alert( JSON.stringify(1) ) // 1
alert( JSON.stringify('test') ) // "test"
alert( JSON.stringify(true) ); // true
alert( JSON.stringify([1, 2, 3]) ); // [1,2,3]
```

JSON은 <u>데이터 교환을 목적</u>으로 만들어진 언어에 종속되지 않는 포맷으로 자바스크립트 특유의 객체 프로퍼티는 `JSON.stringify`가 처리할 수 없다.

`JSON.stringify` 호출 시 무시되는 프로퍼티는 다음과 같다.

* 함수 프로퍼티 (메서드)
* 심볼형 프로퍼티 (키가 심볼인 프로퍼티)
* 값이 `undefined`인 프로퍼티

예시 :

```javascript
let user = {
    sayHi() {
        alert("Hello");
    },
    [Symbol("id")]: 123,
    something: undefined
};
alert( JSON.stringify(user ));	// {}
```

<br>

### replacer로 원하는 프로퍼티만 직렬화하기

`JSON.stringify`의 전체 문법은 다음과 같다.

```javascript
let json = JSON.stringify(value[, replacer, space])
```

* `value` : 인코딩 하려는 값
* `replacer` : JSON으로 인코딩 하길 원하는 프로퍼티가 담긴 배열 또는 함수 `function(key, value)`
* `space` : 서식 변경 목적으로 사용할 공백 문자 수

예시 1 : 

```javascript
let room = {
    number: 23
};

let meetup = {
    title: 'Conference',
    participants: [{name: "John"}, {name: "Alice"}],
    place: room                                    
};

room.occupiedBy = meetup;
```

```javascript
alert( JSON.stringify(meetup, ['title', 'participants']) );
// {"title":"Conference","participants":[{},{}]}
```

예시 2 : 

```javascript
alert( JSON.stringify(meetup, function replacer(key, value) {
  alert(`${key}: ${value}`);
  return (key == 'occupiedBy') ? undefined : value;
}));

/* replacer 함수에서 처리하는 키:값 쌍 목록
:             [object Object]
title:        Conference
participants: [object Object],[object Object]
0:            [object Object]
name:         John
1:            [object Object]
name:         Alice
place:        [object Object]
number:       23
*/

```

<br>

### space로 가독성 높이기

`JSON.stringify(value, replacer, space)`의 세 번째 인수 `space`는 가독성을 높이기 위해 중간에 삽입해 줄 공백 문자 수를 나타낸다. 

예시 : `space = 2`

```javascript
let user = {
  name: "John",
  age: 25,
  roles: {
    isAdmin: false,
    isEditor: true
  }
};

alert(JSON.stringify(user, null, 2));
/* 공백 문자 두 개를 사용하여 들여쓰기함:
{
  "name": "John",
  "age": 25,
  "roles": {
    "isAdmin": false,
    "isEditor": true
  }
}
*/
```

<br>

### JSON.parse

`JSON.parse`를 사용하면 `JSON`으로 인코딩된 객체를 다시 객체로 디코딩 할 수 있다.

문법 : 

```javascript
let value = JSON.parse(str, [reviver]);
```

예시 :

```javascript
let userData = '{
	"name": "John",
    "age": 35,
    "isAdmin": false,
    "friends": [0, 1, 2, 3]
}';
let user = JSON.parse(userData);

alert( user.friends[1] ); // 1
```