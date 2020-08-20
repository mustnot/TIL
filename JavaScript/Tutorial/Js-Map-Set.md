# Javascript Data Types - Map & Set

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용으로 Part 1의 Data Types을 정리한 글이다.

<br>

## 맵 (map)

`map`은 키가 있는 데이터를 저장한다는 점에서 `객체`와 유사하다. 하지만 `map`은 키에 다양한 자료형을 허용한다는 점에서 차이가 있다. 맵의 주요 메서드와 프로퍼티를 살펴보면 다음과 같다.

* `new Map()`
* `map.set(key, value)` : `key`를 이용해 `value`를 저장
* `map.get(key)` : `key`에 해당하는 값을 반환. `key`가 존재하지 않으면, `undefined`를 반환한다.
* `map.has(key)` : `key`가 존재하면 `true`, 존재하지 않는다면 `false`를 반환한다.
* `map.delete(key)` : `key` 제거
* `map.clear` : 맵 안의 모든 요소를 제거한다.
* `map.size`

예시 :

```javascript
let map = new Map();

map.set('1', 'str1');
map.set(1, 'num1');
map.set(true, 'bool1');

alert( map.get(1) );		// num1
alert( map.get('1') );		// str1
alert( map.size );			// 3
```

> ℹ️ `map[key]`는 `Map`을 쓰는 올바른 방법이 아니다.
>
> `map[key] = 2`로 값을 설정하는 것과 같이 `map[key]`를 사용할 수 있다. 하지만 이 방법은 `map`을 일반 객체처럼 취급하기 때문에 개발자로 하여금 혼란스러울 수 있으니, `set`과 `get`를 사용하자.

#### 맵은 객체 또한 키로 허용한다.

예시 :

```javascript
let john = { name: "John" };
let visitsCountMap = new Map{};

visitsCountMap[john] = 123;
alert( visitsCountMap.get(john) ); // 123
```

객체를 키로 사용할 수 있다는 점은 `map`의 가장 중요한 기능 중 하나이다.

<br>

### 맵의 요소에 반복 작업하기

다음 `map`의 메서드는 각 요소에 반복 작업을 할 수 있게 해준다.

* `map.keys()` : 각 요소의 키를 모은 반복 가능한(iterable) 객체를 반환한다.
* `map.values()` : 각 요소의 값을 모은 이터러블 객체를 반환한다.
* `map.entries()` : 요소의 `[키, 값]` 을 한 쌍으로 하는 이터러블 객체를 반환하고, 이 객체는 `for..of` 반복문의 기초로 쓰인다.

예시 :

```javascript
let recipeMap = new Map([
    ['cucumber', 500],
    ['tomatoes', 350],
    ['onion', 50]
]);

for (let vegetable of recipeMap.keys()) {
    console.log(vegetable);
};
for (let amount of recipeMap.values()) {
    console.log(amount);
};
for (let entry of recipeMap) {
    console.log(entry);
};
for (let entry of recipeMap) {
    let vegetable = entry[0];
    let amount = entry[1];
    console.log(vegetable, amount);
};
```

여기에 `map`은 `array`와 유사하게 내장 메서드인 `forEach`도 지원한다.

```javascript
Map.forEach( (value, key, map) => {
  alert(`${key}: ${value}`);
});
```

<br>

### 객체를 맵으로, 맵을 객체로 바꾸기

#### Object.entries : 객체를 맵으로 바꾸기

```javascript
let obj = {
    name: "John",
    age: 30
};
let map = new Map(Object.entries(obj));
alert( map.get('name') ); // John
```

#### Object.fromEntries : 맵을 객체로 바꾸기

```javascript
let prices = Object.fromEntries([
  ['banana', 1],
  ['orange', 2],
  ['meat', 4]
]);
alert(prices.orange); // 2
```

<br>

<br>

## 셋 (set)

`set` 은 중복을 허용하지 않는 값을 모아놓은 특별한 컬렉션으로 키가 없는 값이 저장된다.

* `new Set(iterable)`

* `set.add(value)`
* `set.delete(value)`
* `set.has(value)`
* `set.clear()`
* `set.size()`

셋 내 동일한 값이 있다면, `set.add(value)`를 아무리 호출하더라도 아무런 반응이 없을 것이다. 셋 내의 값에는 중복값이 허용되지 않기 때문인데, 예를 들어 방문자 방명록을 만든다고 가정하자. 한 방문자가 여러 번 방문해도 방문자를 중복해서 기록하지 않겠다고 결정한 상황이다.

예시 :

```javascript
let visitors = new Set();

let john = { name: "John"}
let pete = { name: "Pete"}
let mary = { name: 'Mary'}

visitors.add(john);
visitors.add(pete);
visitors.add(mary);
visitors.add(john);
visitors.add(mary);

alert( visitors.size ); // 3

for (let user of visitors) {
    console.log(user.name);
};
```

<br>

### Set의 값에 반복 작업하기

`for...of` 나 `forEach` 를 사용하면 `set`의 값을 대상으로 반복 작업을 수행할 수 있다..

예시 :

```javascript
let fruits = new Set(["Orange", "Apple", "Banana"]);

for (let fruit of fruits) {
    console.log( fruit );
};

fruits.forEach(value => console.log(value));
/*
fruits.forEach((value, valueAgain, set) => {
	alert(value);
});
*/
```

`set`에도 `map`과 마찬가지로 반복 작업을 위한 메서드가 존재한다.

- `set.keys()` / `set.values()` / `set.entries()` : `map`과 동일하다.