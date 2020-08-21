# Javascript Data Types - Date

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용으로 Part 1의 Data Types을 정리한 글로 그 중 Date 객체에 대해 작성한 글입니다.

<br>

## Date 객체와 날짜

`Date` 객체를 활용하면 생성 및 수정 시간을 저장하거나 시간 측정 혹은 현재 날짜를 출력하는 용도 등에 활용할 수 있습니다.

예시 :

* `new Date() = 현재 날짜 및 시간`
* `new Date(0) = "1970-01-01"`
* `new Date("2020-01-01") = "Wed Jan 01 2020 09:00:00 GMT+0900 (대한민국 표준시)"`

* `new Date(year, month, date, hours, minutes, seconds, ms)`

<br>

### 날짜 구성요소 얻기

`Date` 객체의 메서드를 사용하면 연, 월, 일 등의 값을 얻을 수 있다.

**아래 메서드들은 모두 현지 시간 기준 날짜 구성요소를 반환**하기 때문에 `getUTCFullYear()`과 같이 `UTC`를 붙일 경우 표준시 기준의 날짜 구성 요소를 반환한다.

* `getFullYear()` : 연도(네 자릿수) 반환
* `getMonth()`
* `getDate()` : 날짜 반환
* `getHours(), getMinutes(), getSeconds(), getMilliseconds()`
* `getDay()` : 요일 반환
* `getTime()` : 주어진 일시와 1970-01-01 00:00:00.00 사이의 간격(밀리초 단위)인 타임스탬프 반환
* `getTimezoneOffset()` : 현지 시간과 표준 시간의 차이를 반환한다.

> ⚠️ `getYear()` 말고 `getFullYear()` 를 사용하자.
>
> 여러 자바스크립트 엔진에서는 더는 사용되지 않는(deprecated) 비표준 메서드 `getYear()` 를 구현하고 있다. 하지만, 이 메서드는 두 자릿수 연도를 반환하는 경우가 있기 때문에 절대 사용해서는 안된다. 만약 연도 정보를 얻고 싶다면, `getFullYear()` 를 사용하는 것을 추천한다.

<br>

### 날짜 구성요소 설정하기

아래 메서드를 사용하면 날짜 구성요소를 설정할 수 있다.

- `setFullYear(year, [month], [date])`
- `setMonth(month, [date])`
- `setDate(date)`
- `setHours(hour, [min], [sec], [ms])`
- `setMinutes(min, [sec], [ms])`
- `setSeconds(sec, [ms])`
- `setMilliseconds(ms)`
- `setTime(milliseconds)`

`setTime()`을 제외한 모든 메서드는 `setUTCHours()` 같이 표준시에 따라 날짜 구성 요소를 설정해주는 메서드가 존재한다. `setHours`와 같은 메서더는 여러 개의 날짜 구성요소를 동시에 설정할 수 있는데, 메서드의 인수에 없는 구성요소는 변경되지 않는다.

예시 :

```javascript
let today = new Date(); // Fri Aug 21 2020 21:34:35 GMT+0900
today.setHours(0);
alert(today); // Fri Aug 21 2020 00:34:35 GMT+0900

today.setHours(0, 0, 0, 0);
alert(today); // Fri Aug 21 2020 00:34:35 GMT+0900
```

<br>

### 자동 고침

`Date` 객체엔 자동 고침(autocorrection) 이라는 유용한 기능이 있어 만약 범위를 벗어나는 값을 설정하려고 하면 자동 고침 기능이 활성화되면서 자동으로 수정한다.

예시 :

```javascript
let date = new Date(2013, 0, 32); // 2013년 1월 32일은 없습니다.
alert(date); // 2013년 2월 1일이 출력됩니다.
```

<br>

### Date.parse와 문자열

메서드 `Date.parse(str)`를 사용하면 문자열에서 날짜를 읽어올 수 있다. 
(단, 문자열의 형식은 `YYYY-MM-DDTHH:mm:ss.sssZ` 와 같아야한다.)

예시 :

```javascript
let ms = Date.parse('2012-01-26T13:51:50.417-07:00');
alert(ms); // 1327611110417
let date = new Date(ms);
alert(date); // Fri Jan 27 2012 05:51:50 GMT+0900
```

