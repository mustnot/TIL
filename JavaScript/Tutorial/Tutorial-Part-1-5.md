# Javascript Tutorial 1 - Data Types

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용으로 Part 1의 Data Types을 정리한 글이다.

<br>

## 원시값의 메서드 (Methods of primitives)

자바스크립트는 원시값(문자열, 숫자 등)을 마치 객체처럼 다룰 수 있게 해줍니다. 말그대로 객체에서처럼 메서드를 호출할 수 있는데, 그렇다고 원시값이 객체는 아니라는 것을 명심하자.

<br>

### 원시값을 객체처럼 사용하기

* 문자열이나 숫자와 같은 원시값을 다루어야 하는 작업이 많은데, 메서드를 사용하면 작업을 수월하게 할 수 있을 것 같다는 생각이 듭니다.
* 그런데 원시값은 가능한 한 빠르고 가벼워야 합니다.

자바스크립트는 위와 같은 모순적인 상황을 해결하기 위해 다음과 같은 해결책을 제안했습니다.

1. 원시값은 원시값 그대로 남겨둬 단일 값 형태를 유지
2. 문자열, 숫자, 불린, 심볼의 메서드와 프로퍼티에 접근할 수 있도록 언어 차원에서 허용
3. 이를 가능하게 하기 위해, 원시값이 메서드나 프로퍼티에 접근하려 하면 추가 기능을 제공해주는 특수한 객체, "원시 래퍼 객체(object wrapper)"를 만들어 주고 객체는 삭제된다.

"래퍼 객체"는 원시 타입에 따라 종류가 다양한데, 각 래퍼 객체는 원시 자료형의 이름을 그대로 차용해, `String, Number, Boolean, Symbol` 이라고 부르고, 래퍼 객체마다 제공하는 메서드 역시 다르다.

```javascript
// string
let str = "Hello";
alert( str.toUpperCase() ); // HELLO;
alert( str ); // Hello;
```

동작 방식은 다음과 같다.

1. 문자열 `str`은 `String` 타입의 원시값으로, 원시값의 프로퍼티인 `toUpperCase()`에 접근하는 순간 특별한 객체가 만들어진다.
2. 메서드가 실행되고, 새로운 문자열을 반환
3. 반환된 이후에 1에서 생성되었던 특별한 객체는 파괴되고, 원시값인 `str` 만 다시 남는다.

<br>

<br>

## 숫자형

모던 자바스크립트는 숫자를 나타내는 두 가지 자료형을 지원한다.

1. 일반적인 숫자 '배정밀도 부동소수점 숫자'로 알려진 64bit 형식의 [IEEE-754](https://en.wikipedia.org/wiki/IEEE_754-2008_revision)에 저장
2. 임의의 길이를 가진 정수는 **Bigint** 숫자로 나타낼 수 있는데, 일반적인 숫자는 `2^53`이상 이거나 `-2^53`이하일 수 없다는 제약 때문에 BigInt라는 새로운 자료형이 만들어짐

<br>

### 숫자를 입력하는 다양한 방법

```javascript
let billion = 1000000000;
let billion = 1e9;	// 1 * 10^9
```

### 16진수, 2진수, 8진수

```javascript
// 16진수 ff와 FF는 대소문자를 구분하지 않는다.
console.log( 0xff ); // 255
console.log( 0xFF ); // 255
// 2진수
console.log( 0b11111111 ) // 255
// 8진수
console.log( 0o377 ) // 255
```

### toString(base)

`num.toString(base)` 메서는 `base` 진법으로 `num` 을 표현한 후 이를 문자형으로 변환해 반환한다.

```javascript
let num = 255;
console.log( num.toString(16) ); // ff
console.log( num.toString(2) ); // 111111111
```

<br>

<br>

## 문자열

> 메서드만 알아보고 넘어가는게 좋아보여 코드만 작성했다.

```javascript
// 문자열 길이 str.length - length는 프로퍼티이다.
console.log( 'this is string'.length )	// 14

// 특정 글자에 접근하기
console.log( 'this is string'[0] )		// t

// 문자열의 불변성 - 특정 글자만 변경 불가능
let str = 'this is string';
str[0] = 'T';
console.log( str ); // this is string;

// 대-소문자 변경하기
console.log( str.toUpperCase() ); 		// THIS IS STRING
console.log( str.toLowerCase() );		// this is string

// 부분 문자열 찾기
console.log( str.indexOf('this') );		// 0
console.log( str.indexOf('Interger') );	// -1

// include, startsWith, endsWith
console.log( str.include('this') ); 	// true
console.log( str.startsWith('this') );	// true
console.log( str.endsWith('string') );	// true

// 부분 문자열 추출하기 (end-1 까지 반환)
console.log( str.slice(0, 4) );		// this
console.log( str.substring(0, 7) );	// this is
console.log( str.substr(0, 6) );	// this i

// etc
console.log( "z".codePointAt(0) ); 	// 122
console.log( "Z".codePointAt(0) ); 	// 90

console.log( String.fromCodePoint(90) ); 	// Z

console.log( '\u005a' );	// Z
```

<br>

<br>

## 배열

키를 사용해 식별할 수 있는 값을 담은 컬렉션은 주로 객체라는 자료구조를 이용해 저장하는데, 객체만으로도 충분히 다양한 작업을 할 수 있다. 하지만 개발을 하다보면 첫 번째 요소, 두 번째 요소, .. 등과 같이 순서가 있는 컬렉션이 필요 할 때가 생긴다. 사용자나 물건, HTML 요소 목록 같이 순서를 만들어 정렬하기 위해서 필요하다. 

순서가 있는 컬렉션을 다뤄야할 때 객체를 사용하면 순서와 관련된 메서드가 없어 그다지 편리하지 않다. 객체는 태생이 순서를 고려하지 않고 만들어진 자료 구조이기 때문에 객체를 이용하여 새로운 프로퍼티를 기존 프로퍼티 사이에 끼워 넣는 것도 불가능하다.

<br>

### 배열 선언

* 배열은 길이의 제약이 없는 자료구조로 추가하고 싶다면, 언제든지 데이터를 추가할 수 있다.
* 자료형에 제약이 없어, 배열 내부에 여러가지 자료형이 포함될 수 있다.
    `ex) [String, Function, Integer, Object]`

```javascript
let arr = new Array();
let arr = [];
```

```javascript
let fruits = ["사과", "오렌지", "자두"];
console.log( fruits[0] );	// 사과 - 인덱스는 0부터 시작
console.log( fruits[1] );	// 오렌지
console.log( fruits[2] );	// 자두
```

```javascript
// 배열 추가
fruits[3] = "레몬";
console.log( fruits[3] );	// 레몬

// length
console.log( fruits.length );	// 3
```

<br>

### pop-push와 shift-unshift

자료구조 [큐(queue)](https://en.wikipedia.org/wiki/Queue_(abstract_data_type))는 배열을 사용해 만들 수 있는 대표적인 자료구조로, 배열과 마찬가지로 순서가 있는 컬렉션을 저장하는 데 사용한다. 큐에서 사용하는 주요 연산은 다음과 같다.

* `push` :  맨 끝에 요소를 추가
* `shift` : 제일 앞 요소를 꺼내 제거한 후 남아있는 요소들을 앞으로 밀어준다. 두 번째 요소가 첫 번째 요소가 된다.

배열엔 두 연산을 가능케 해주는 내장 메서드인 `push` 와 `pop` 이 있다. 화면에 순차적으로 띄울 메시지를 비축해 놓을 자료 구조를 만들 때 큐를 사용하는 것처럼 큐는 실무에서 상당히 자주 쓰이는 자료구조입니다. 이런 배열은 큐 이외에 [스택(stack)](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))이라 불리는 자료구조를 구현할 때에도 사용한다.

* `push` : 요소를 스택 끝에 집어 넣는다.
* `pop` : 스택 끝 요소를 추출한다.

스택을 사용하면 가장 나중에 집어넣은 요소가 먼저 나오는데, 이런 특징을 줄여서 후입선출(LIFO)라고 부르고, 반대로 큐를 사용하면 먼저 집어 넣은 요소가 먼저 나오기 때문에 선입선출(FIFO) 자료구조라 부른다.

이렇게 처음이나 끝에 요소를 더하거나 빼주는 연산을 하는 자료구조를 컴퓨터 과학 분야에서는 **deque**라고 부른다.

> ℹ️ 배열은 객체이므로 원하는 프로퍼티를 추가해도 문제가 발생하지 않는다.
>
> ```javascript
> let fruits = [];
> fruits.age = 25;
> console.log( fruits.age );	// 25
> ```

<br>

### 반복문

`for`문은 배열을 순회할 때 쓰는 가장 오래된 방법이다.

```javascript
// index를 사용하는 방법
let arr = ["사과", "오렌지", "배"]

for (i = 0, i < arr.length, i++) {
    console.log( arr[i] );
}

// for..of를 사용하는 방법
for (let fruit of fruits) {
    console.log( fruit );
}
```

<br>

<br>

## 배열과 메서드

배열은 이전에 공부한 `push`, `pop`, `shift`, `unshift`를 제외하고 다양한 메서드를 지원한다.

### splice

배열을 이용하다보면, 배열의 요소를 하나만 지우고 싶을 때가 있는데, `delete`를 사용하는 방법이 있다.

```javascript
let arr = ["this", "is", "array"];
delete arr[1];

alert( arr[1] );	// undefined ? 지워진 자리에 알아서 채워지진 않는다.
alert( arr.length );	// 3
```

`delete`를 사용하게 되면 위에서 처럼 기존에 자리를 그대로 차지하고 있는 문제가 있다. 지우면 알아서 그 뒤에 있던 요소가 앞으로 오면서 자리를 차지하는 것이 아니다. 이럴 때에는 `splice` 메서드를 사용하면 된다. 문법은 다음과 같다.

```javascript
arr.splice(index[, deleteCount, elem1, ... elemN])
```

* `index`: 조작을 가할 첫 번째 요소
* `deleteCount`: 제거하고자 하는 요소의 개수
* `elem1, ..., elemN`: 배열에 추가할 요소

예시

```javascript
let arr = ["I", "study", "JavaScript"];

arr.splice(1, 1);
alert( arr );	// ["I", "JavaScript"]
```

```javascript
let arr = ["I", "study", "JavaScript", "right", "now"];

arr.splice(0, 3, "Let's", "dance");
alert( arr ) // now ["Let's", "dance", "right", "now"]
```

이렇듯 삭제할 첫 번째 요소부터 제거하고자 하는 요소의 개수를 지정하면 해당 요소들은 제거하고, 추가하는 것 역시 가능하다. 또한, `splice` 된 데이터는 다른 배열 객체로 반환되기도 한다.

```javascript
let arr = ["I", "study", "JavaScript", "right", "now"];
let removed = arr.splice(0, 2);
alert( removed );	// "I", "study"
```

<br>

### slice

`arr.slice`는 `arr.splice`와 유사해 보이지만 훨씬 간단하다. 문법을 보자.

```javascript
arr.slice([start], [end])
```

`slice` 메서드는 `start` 인덱스부터 `end` 를 제외한 인덱스까지의 요소를 복사한 새로운 배열을 반환한다. `start`와 `end`는 둘 다 음수일 수 있다.

예시 :

```javascript
let arr = ["t", "e", "s", "t"]
alert( arr.slice(1, 3) );	// e, s
alert( arr.slice(-2) );		// s, t
```

<br>

### concat

`arr.concat`은 기존 배열의 요소를 사용해 새로운 배열을 만들거나 기존 배열에 요소를 추가하고자 할 때 사용할 수 있습니다.

```javascript
arr.concat(arg1, arg2, ...)
```

인수에는 배열이나 값이 올 수 있는데, 개수에는 제한이 없다. 메서드를 호출하면 `arr`에 속한 모든 요소와 `arg1, arg2, ...`에 속한 모든 요소들 한데 모은 새로운 배열이 반환된다.

예시 :

```javascript
let arr = [1, 2];

alert( arr.concat([3, 4]) ); // 1,2,3,4
alert( arr.concat([3, 4], [5, 6]) ); // 1,2,3,4,5,6
alert( arr.concat([3, 4], 5, 6) ); // 1,2,3,4,5,6
```

`concat` 메서드는 제공받은 배열의 요소를 복사해 활용하는데, 객체가 인자로 넘어오면, 객체는 분해되지 않고 통으로 복사되어 더해진다.

```javascript
let arr = [1, 2];
let arrayLike = {
  0: "something",
  length: 1
};

alert( arr.concat(arrayLike) ); // 1,2,[object Object]
```

<br>

### forEach로 반복작업 하기

`arr.forEach`는 주어진 함수를 배열 요소 각각에 대해 실행할 수 있게 해준다.

문법 :

```javascript
arr.forEach(function(item, index, array) {
    // 요소에 무언가를 할 수 있습니다.
})
```

예시 :

```javascript
// for each element call alert
["Bilbo", "Gandalf", "Nazgul"].forEach(alert);
// for each element call alert with more informations e.g index
["Bilbo", "Gandalf", "Nazgul"].forEach((item, index, array) => {
  alert(`${item} is at index ${index} in ${array}`);
});
// ${item} : 요소 값 / ${index} : 인덱스 / ${array} : 배열 전체
```

<br>

### 배열 탐색하기

#### indexOf, lastIndexOf 와 includes

```javascript
let arr = [1, 0, false];

alert( arr.indexOf(0) ); // 1
alert( arr.indexOf(false) ); // 2
alert( arr.indexOf(null) ); // -1

alert( arr.includes(1) ); // true
```

#### find 와 findIndex

객체로 이루어진 배열이 있다고 가정할 때, 특정 조건에 부합하는 객체를 배열 내에서 찾을 수 있는 메서드로 `arr.find(fn)` 이 있다.

```javascript
// arr.find(fn)
let result = arr.find(function(item, index, array) {
  // true가 반환되면 반복이 멈추고 해당 요소를 반환합니다.
  // 조건에 해당하는 요소가 없으면 undefined를 반환합니다.
});
```

```javascript
let users = [
  {id: 1, name: "John"},
  {id: 2, name: "Pete"},
  {id: 3, name: "Mary"},
  {id: 4, name: "Jery"},  
];
let user = users.find(function(item, index, array) {
    if (item.name.startsWith('J')) {
        return true
    };
})
alert( user ); // {id: 1, name: "John"}
```

#### filter

`find` 메서드는 함수의 반환 값이 `true`인 <u>단 하나</u>의 원소를 찾는 반면, 조건을 충족하는 요소가 여러 개일 때, 모두 반환하는 `arr.filter(fn)`이 있다.

```javascript
let users = [
  {id: 1, name: "John"},
  {id: 2, name: "Pete"},
  {id: 3, name: "Mary"},
  {id: 4, name: "Jery"},  
];
let j_user = users.filter(function(item, index, array) {
    if (item.name.startsWith('J')) {
        return true
    };
})
alert( j_user ); // [{id: 1, name: "John"}, {id: 4, name: "Jery"}]
```

<br>

### 배열을 변형하는 메서드

배열을 변형시키거나 요소를 재정렬해주는 메서드에 대해 알아본다.

#### map

`arr.map`은 유용성과 사용 빈도가 아주 높은 메서드 중 하나로 배열 요소 전체를 대상으로 함수를 호출하고, 함수 호출 결과를 배열로 반환해준다.

문법:

```javascript
let result = arr.map(function(item, index, array) {
   // 요소 대신 새로운 값을 반환합니다. 
});
```

예시:

```javascript
let lenghts = ["Bilbo", "Gandalf", "Nazgul"].map(item => item.length);
alert( lengths );		// 5, 7, 6
```

#### sort(fn)

`arr.sort(fn)`은 배열의 요소를 정렬해주고, 배열 자체가 변경된다는 주요점이 있다. 메서드를 호출하면 재정렬된 배열을 반환하는데, 이미 `arr` 자체가 수정되었기 때문에 반환 값을 잘 사용되지 않는다.

예시:

```javascript
let arr = [1, 15, 2];
arr.sort();
alert(arr);	// 1, 15, 2
```

예시를 보면 `sort()` 했는데도 불구하고 `1, 2, 15` 가 아닌 `1, 15, 2` 로 변형되었는데, 이는 문자열로 취급되어 재정렬되었기 때문이다. 이는 아래처럼 변경 가능하다.

```javascript
// compareNumeric 함수를 만들어 사용하는 방법
function compareNumeric(a, b) {
  if (a > b) return 1;
  if (a == b) return 0;
  if (a < b) return -1;
}
arr.sort(compareNumeric);

// 간결한 방법
let arr = [ 1, 2, 15 ];

arr.sort(function(a, b) { return a - b; });
```

#### reverse

`arr.reverse` 는 `arr`의 요소를 역순으로 정렬 시켜주는 메서드다.

```javascript
let arr = [1, 2, 3, 4, 5];
arr.reverse();
alert( arr ); // [5, 4, 3, 2, 1]
```

#### split과 join

메시지 전송 어플리케이션을 만들고 있다고 가정할 때, 수신자가 여러 명일 경우 발신자는 쉼표로 각 수신자를 구분한다. 예를 들어 `John, Pete, Mary` 같이, 개발자는 긴 문자열 형태의 수신자 리스트를 배열 형태로 전환해 처리하고 싶을텐데, 입력받은 문자열을 어떻게 배열로 바꿀 수 있을까?

`str.split(delim)` 은 구분자(delimiter) 를 기준으로 문자열을 쪼개준다.

```javascript
let names = 'Bilbo, Gandalf, Nazgul';
let arr = names.split(', ');

arr.forEach(alert);		// Bilbo, Gandalf, Nazgul
```

`split` 메서드는 두 번 째 인수로 숫자를 받을 수 있는데, 배열의 길이를 제한해주어 길이를 넘어서는 요소는 무시한다.

```javascript
let names = 'Bilbo, Gandalf, Nazgul';
let arr = names.split(', ', 2);

arr.forEach(alert);		// Bilbo, Gandalf
```

> ℹ️ 문자열을 글자 단위로 분리하기
>
> ```javascript
> let str = "test";
> alert( str.split('') );	// t,e,s,t
> ```

#### reduce와 reduceRight

`forEach`, `for`, `for...of` 를 사용하면 배열 내 요소를 대상으로 반복 작업을 할 수 있고, 이런 반복 작업을 수행하고 결과물을 새로운 배열 형태로 얻으려면 `map`을 사용하면 된다. 이런 작업을 하는 유사한 메서드가 있는데, 바로 `arr.reduce` 와 `arr.reduceRight` 이다. 

문법 :

```javascript
let value = arr.reduce(function(accumulator, item, index, array) {
    // ...
}, [initial]);
```

* `accumulator` : 이전 함수 호출의 결과 `initial`은 함수 최초 호출 시 사용되는 초기값을 나타냄 (옵션)
* `item, index, array` : 동일

이전 함수 호출 결과는 다음 함수를 호출 시 첫 번째 인수(`previousValue`)로 사용된다.

```javascript
let arr = [1, 2, 3, 4, 5];

let result = arr.reduce((sum, current) => sum + current, 0);

alert(result); // 15
```

일종에 `accumulator`는 앞서 호출했던 결과들이 누적되어 저장되는 **누산기**라고 생각하면 되는데, 마지막까지 호출되면 `reduce`의 반환값이 된다.

<br>

### Array.isArray로 배열 여부 알아내기

자바스크립트에서 배열은 독립된 자료형으로 취급되지 않고 객체형에 속하기 때문에 일반적인 `typeof`로는 일반 객체와 배열을 구분할 수가 없다.

```javascript
alert(typeof {}); // object
alert(typeof []); // object
```

그래서 배열은 배열인지 아닌지를 감별해내는 특별한 메서드가 있는데, 바로 `Array.isArray(value)` 라는 메서드가 있다.

```javascript
alert(Array.isArray({})); // false
alert(Array.isArray([])); // true
```

<br>

