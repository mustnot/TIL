# Webpack

> webpack, babel 등을 설치해서 React는 띄워봤지만, 정확히 어떤 역할을 하는지 알아야 나중에 어디에서 문제가 생겼는지 정확히 판단할 수 있을 것 같아 공부하기로 하였다. 김정한님의 블로그를 참고했고, 공부하면서 그동안 개발하면서 겪었던 상황들을 추가로 적으면서 이해하려고 노력했다. (좋은 글 감사합니다)

<br>

## 배경

Javascript는 대략 ES2015부터 문법 수준의 모듈을 지원하기 시작했다. 우선 `import/export` 구문이 없던 모듈 이전 상황을 살펴보자.

예시 :

```javascript
// math.js
function sum(a, b) {
  return a + b
}
```

```javascript
// app.js
sum(1, 2) // 3
```

이렇게 두 개의 `js` 파일이 있다고 가정할 때, `calc.js`에서 `sum`이라는 함수를 쓰기 위해서는 언제나 `sum.js`와 `calc.js`는 하나의 HTML 파일 안에서 로딩해야 했다. 하지만 문제는 `sum`이라는 함수가 전역 공간에 노출되어, 다른 파일에서도 `sum`이란 이름을 사용한다면 충돌이 일어난다. 

(이런 경우는 자주 없지만, 가능성이 없지 않다) 예를 들어, `import/export` 구문이 없기 때문에 `sum`이라는 함수를 같은 HTML 파일 안에 로딩하기가 싫어서 또 다른 `js` 파일에 `sum`이라는 함수를 만들었다가, 다른 개발자 혹은 실수로 인해서 `sum.js`를 한 HTML 파일에 로딩 시키면서 같은 `sum`이라는 함수가 2개 생겨나면서 에러가 발생할 수 있다. 물론 2개의 함수가 동일하다면 문제가 될리는 없지만, 개발을 하다보면 이것보다 더 복잡한 함수를 많이 사용하기 때문에 발생할 수 있다.

<br>

### IIFE 방식의 모듈

이런 문제를 예방하기 위해 **스코프**를 사용한다. 함수 스코프를 만들어 외부에서 안으로 접근하지 못하도록 공간을 격리하는 것을 말하는데, 스코프 안에서는 자신만의 이름 공간이 존재하므로 스코프 외부와 이름 충돌을 막을 수 있다.

예시 :

```javascript
// math.js
var math = math || {}

;(function() {
  function sum(a, b) {
    return a + b
  }
  math.sum = sum
})()
```

이런 방법으로 `sum` 함수를 **즉시실행함수**로 감싸 다른 파일에서 안으로 접근할 수 없도록 했다. 심지어 같은 파일일지라도 접근할 수 없다. 자바스크립트 함수 스코프의 특징으로 `sum`이란 이름은 즉시실행함수 안에 감추어졌기 때문에 외부에서는 같은 이름을 사용해도 괜찮고, 전역에 등록한 `math`라는 이름 공간만 잘 활용하면 된다.

<br>

### 다양한 모듈 스펙

이러한 방식으로 자바스크립트 모듈을 구현하는 대표적인 명세로 **AMD**와 **CommonJS**가 있다.

<u>**CommonJS**</u>는 자바스크립트를 사용하는 모든 환경에서 모듈을 하는 것이 목표이다. `exports` 키워드로 모듈을 만들고 `require()` 함수로 불러 들이는 방식이다. 대표적으로 Server-side platform인 Node.js에서 이를 사용한다. 

<u>**AMD**</u>(Asynchronous Module Definition)는 비동기로 로딩되는 환경에서 모듈을 사용하는 것이 목표로 주로 브라우저 환경을 말한다.

<u>**UMD**</u>(Universal Module Definition)는 **AMD**기반으로 **CommonJS** 방식까지 지원하는 통합 형태이다.

이런 각 커뮤니티에서는 각자의 스펙을 제안하다가 <u>**ES2015에서 표준 모듈 시스템**</u>을 내놓았는데, 지금은 바벨(babel)과 웹팩(webpack)을 이용해 모듈 시스템을 사용하는 것이 일반적이다.

예시 :

```javascript
// math.js
export function sum(a, b) {
  return a + b
}
```

```javascript
// app.js
import * as math from "./math.js"
math.sum(1, 2);
```

<br>

### 브라우저의 모듈 지원

**ES2015에서 표준 모듈 시스템**을 내놓았지만, 안타깝게도 모든 브라우저에서 모듈 시스템을 지원하는 것은 아니다. 대표적으로는 인터넷 익스플로러가 있어 여전히 모듈을 사용하지 못한다.

예시 :

```html
<!-- index.html -->
<script type="module" src="app.js"></script>
```

예시와 같이 `<script>` 태그로 로드할 때에 `type="module"`을 사용하면 `app.js`는 모듈을 사용할 수 있다. (잊지 말자)

<br>

<br>

## 엔트리/아웃풋

> ℹ️ 웹팩의 정의가 여기서부터 나온다 !

[웹팩](https://webpack.js.org/)은 여러개 파일을 하나의 파일로 합쳐주는 번들러(bundler)다. 하나의 시작점(entry point)으로부터 의존적인 모듈을 전부 찾아내서 하나의 결과물을 만들어 내는데, `app.js`부터 시작해 `math.js`를 찾은 뒤 하나의 파일을 만드는 방식이다.

(Comment) 그럼 웹팩은 <u>브라우저에 무관</u>하게 모듈을 사용하고 싶어서 하나의 파일을 만들어 모든 브라우저에서 사용할 수 있게 만드는 것을 말하는구나라고 생각하면 된다고 보인다. 대표적인 이유가 역시나 IE 때문이라고 생각되는데, IE의 경우 **ES2015 표준 모듈 시스템**을 사용할 수 없기 때문에 하나의 HTML 파일 안에 모든 js가 포함되어야한다. 방대한 웹 사이트의 경우 그 함수의 개수도 많을 뿐더러 관리하기 쉽지 않기 때문에 웹팩을 사용하여 **하나의 파일**을 만들어 사용한다.

설치 :

```bash
$ npm i -D webpack webpack-cli
```

설치가 정상적으로 완료되면, `node_modules/.bin` 폴더에 실행 가능 명령어가 몇 가지 생기는데, 우리가 설치한  `webpack`과 `webpack-cli`도 추가되었는데, 이 중 하나를 택해 `-h`', `--help` 옵션으로 사용 방법을 확인하자.

```bash
$ node_modules/.bin/webpack -h
$ node_modules/.bin/webpack-cli --help
```













## 참조

* [프론트엔드 개발환경의 이해: 웹팩(기본) - 김정한](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html)
  * 정말 잘 설명되어 있고, 기회가 된다면 나도 이렇게 누군가에게 잘 설명할 수 있는 글을 써보고 싶다.

