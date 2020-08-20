# Javascript Data Types - Number & String

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

