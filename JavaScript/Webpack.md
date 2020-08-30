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

많은 옵션들이 나타나는데, `--mode`, `--entry`, `--output` 세 개 옵션만 사용하면 코드를 하나로 만들 수 있다.

```bash
$ node_modules/.bin/webpack --mode development --entry ./src/app.js --output dist/main.js
```

* `--mode` 는 웹팩 실행 모드를 의미하는데, `development` 는 개발 버전을 의미한다.
  (`development` 외 `none`(모드 설정 안함), `production`(배포 모드) 모드가 있다)
* `--entry` 는 시작점 경로를 지정하는 옵션이다.
* `--output` 은 번들링 결과물을 저장할 위치이다.

위 명령어를 실행하면 다음과 같은 결과를 얻는다.

```bash
$ node_modules/.bin/webpack --mode development --entry ./app.js --output ./main.js
Hash: ce2ee6b36e5835850f81
Version: webpack 4.44.1
Time: 56ms
Built at: 2020. 08. 30. 오후 5:07:42
  Asset      Size  Chunks             Chunk Names
main.js  4.45 KiB    null  [emitted]  null
Entrypoint null = main.js
[./app.js] 52 bytes {null} [built]
[./math.js] 61 bytes {null} [built]
```

이 코드를 `index.html`에 로딩하면 번들링 전과 똑같은 결과를 얻는다.

before :

```html
<script type="module" src="./math.js"></script>
<script type="module" src="./app.js"></script>
```

after :

```html
<script src="./main.js"></script>
```

옵션 중 `--config`를 살펴보자.

```bash
$ node_modules/.bin/webpack --help
# ...
Config options:
  --config           Path to the config file
                     [문자열] [기본: webpack.config.js or webpackfile.js]
  --mode             Enable production optimizations or development hints.
                     [선택: "development", "production", "none"]
```

`config` 옵션은 웹팩 설정파일의 경로를 지정할 수 있는데, 기본 파일명은 `webpack.config.js` 혹은 `webpackfile.js`로 지정한다.

```javascript
// webpack.config.js
const path = require("path");

module.exports = {
  mode: "development",
  entry: {
    main: "./src/app.js",
  },
  output: {
    filename: "[name].js",
    path: path.resolve("./dist"),
  },
};
```

처음에는 이해하기 조금 난해했는데, 지금보니 조금이라도 스스로 써볼 수 있을 것 같다.

* `mode` : `development` 적용
* `entry` : 어플리케이션 진입점인 `app.js`로 설정
* `output` : `[name]` 은 `entry`에 추가한 `main`이 문자열로 들어온다.

웹팩 실행을 위한 NPM 커스텀 명령어를 추가할 수 있다.

```javascript
// package.json
{
  "script": {
    "build": "./node_modules/.bin/webpack"
    // "build": "webpack" 으로 작성해도 된다. 이미 webpack이 설치되어 있어 반영된다.
  }
}
```

`webpack.config.js`에 모든 옵션을 옮겼기 때문에 단순히 `webpack` 명령어만 실행한다. 여기서부터는 `npm run build`로 웹팩 작업을 실행할 수 있다.

<br>

<br>

## 로더

웹팩은 모든 파일을 모듈로 바라본다. 무슨 뜻이냐면, 자바스크립트로 만든 모듈 뿐만 아니라, 스타일시트, 이미지, 폰트까지도 전부 모듈로 보기 때문에 `import` 구문을 사용하면 자바스크립트 코드 안으로 가져올 수 있다.

(여기까지 읽으면서 뭔가 아리송하다고 생각했다) 이것이 가능한 이유는 웹팩의 <u>**로더**</u> 덕분인데, 로더는 타입스크립트 같은 다른 언어를 자바스크립트 문법으로 변환해주거나 이미지를 data URL 형식의 문자열로 변환한다. 

<br>

### 커스텀 로더 만들기

로더를 사용하기 전 동작 원리를 이해하기 위해 로더를 직접 만들어보면 다음과 같다.

```javascript
// myloader.js
module.exports = function myloader(content) {
  console.log( "myloader가 동작함" );
  return content;
}
```

위와 같이 로더를 함수로 만들었는데, 로더가 읽은 파일의 내용이 함수 인자 `content`로 전달되어. 로드가 동작하는지 확인하는 용도로 로그만 찍고 인자로 받은 `content`는 그대로 반환한다.

로더를 사용하려면 웹팩 설정 파일의 `module` 객체에 추가하면 된다.

```javascript
// webpack.config.js
module: {
  rules: [{
    test: /\.js$/,		// .js 확장자를 가진 모든 파일
    use: [path.resolve('./myloader.js')]		// 방금 생성한 커스텀 로더
  }]
}
```

`module.rules` 배열에 모듈을 추가하는데 `test`와 `use`로 구성된 객체를 전달한다.

* `test` : 로딩에 적용할 파일을 지정할 수 있다. 위 예시와 달리 파일명 뿐만 아니라 파일 패턴을 정규표현식으로 지정할 수 있는데 위 코드는 `.js` 확장자를 갖는 모든 파일을 처리하겠다는 의미이다.

* `use` : 작성된 패턴에 해당하는 파일에 적용할 로더를 설정하는 부분으로 `myloader.js` 함수의 경로를 지정한다.

<br>

<br>

## 자주 사용하는 로더

로더의 동작 원리를 살펴보고, 자주 사용하는 로더를 살펴보자.

<br>

### css-loader

위에서 설명함과 마찬가지로 웹팩은 자바스크립트 외에도 모든 파일을 모듈로 바라보기 때문에 스타일시트도 `import` 구문으로 불러올 수 있다.

```javascript
// app.js
import "./style.css"
```

```css
// style.css
body {
  background-color: green;
}
```

자바스크립트에서 CSS  파일을 불러와 사용하기 위해서는 CSS를 모듈로 변환하는 작업이 필요한데, `css_loader`가 그러한 역할을 대신한다. 

css-loader 설치

```bash
$ npm i -D css-loader
```

웹팩 설정에 로더 추가

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: \/.css$/,
        use: ["css-loader"],
  		}
    ]
  }
}
```

위와 같이 설정을 완료하면, 웹팩은 엔트리 포인트부터 시작해서 모듈을 탐색하다 CSS 파일을 찾으면 `use`에 작성한 `css-loader`로 처리할 것이다. 

결과 :

```javascript
// main.js
...push([module.i, \"body {\\n  background-color: green;\\n}\\n\", \"\"]);
```

<br>

### style-loader

하지만 css-loader로 변경된 `main.js`를 불러와도 `background-color: green`이 반영되지 않는데, 원인은 css-loader로 처리하면 자바스크립트 코드로 변경되었을 뿐 돔에 적용되지 않았기 때문에 스타일이 적용되지 않는다.

<u>**style-loader**</u>는 자바스크립트로 변경된 스타일을 동적으로 돔에 추가하는 로더로, CSS를 번들링하기 위해서는 css-loader와 style-loader를 함께 사용한다.

style-loader 설치

```bash
$ npm i -D style-loader
```

웹팩 설정에 로더 추가

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ["style-loader", "css-loader"], // style-loader를 앞에 추가한다
      },
    ],
  },
}
```

(주의) <u>배열로 설정하면 뒤에서부터 앞으로 순서대로 로더가 동작한다.</u> 그래서 위와 같이 css-loader를 먼저 적용하고 그 다음 style-loader를 적용해야한다.

<br>

### file-loader

웹팩은 다시 말하지만, CSS 뿐만 아니라 소스코드에서 사용하는 모든 파일을 모듈로 사용하게끔 할 수 있는데, 파일을 모듈 형태로 지원하고 웹팩 아웃풋에 파일을 옮겨주는 것이 있는데, 바로 <u>**file-loader**</u>가 있다. 예를 들어 CSSdㅔ서 url() 함수에 이미지 파일 경로를 지정할 수 있는데, 웹팩은 file-loader를 이용해서 이 파일을 처리한다.

```css
// style.css:
body {
  background-image: url(bg.png)
}
```

배경 이미지를 bg.png 파일로 지정하면, 웹팩은 엔트리 포인트인 `app.js`가 로딩하는 `style.css`를 읽을 것이고, 이 스타일시트는 url() 함수로 bg.png를 사용하는데 이때 file-loader를 동작시킨다.

웹팩 설정에 로더 추가

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.png$/, // .png 확장자로 마치는 모든 파일
        loader: "file-loader", // 파일 로더를 적용한다
      },
    ],
  },
}
```

웹팩이 .png 파일을 발견하면 file-loader를 실행할 것이고, 로더가 동작하고 나면 아웃풋에 설정한 경로로 이미지 파일을 복사한다. 파일을 살펴보면 파일명이 해쉬코드로 변경 되었는데, 캐쉬 갱신을 위한 처리로 보인다.

하지만, 이대로 `index.html`에 로딩하면 브라우저에서는 이미지를 불러오지 못한다. 그 이유는 파일명을 해쉬코드로 변경하고, webpack 설정을 살펴보면 `dist` 폴더로 이동하기 때문에 이미지를 불러오지 못한다. 그래서 아래와 같이 추가적인 설정이 필요하다.

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.png$/, // .png 확장자로 마치는 모든 파일
        loader: "file-loader",
        options: {
          publicPath: "./dist/", // prefix를 아웃풋 경로로 지정
          name: "[name].[ext]?[hash]", // 파일명 형식
        },
      },
    ],
  },
}
```

위와 같이 설정하고 다시 `build` 하면 해쉬 코드로 변경된 파일명이 아닌 css에 작성했던 bg.png 파일로 변경된 것을 볼 수 있다. 

* `publicPath` : `url(./bg.png)` 를 `url(dist/bg.png)` 와 같은 형식으로 변경해준다.
* `name` : 로더가 파일을 아웃풋으로 복사할 때 사용하는 파일 이름으로, 기본적으로 변경되는 해쉬값을 쿼리 스트링으로 옮겨서 `/dist/bg.png?d008e21964e0a2b291c7c499a8f3dae1` 와 같이 파일을 요청하도록 변경된다.

<br>

### url-loader

사용하는 이미지 개수가 많다면 네트웤 리소스를 사용하는 부담이 있고 사이트 성능에 영향을 줄 수 있다. 예를 들어 하나의 페이지에 수 많은 이미지가 있어, 이런 이미지들을 한 대의 서버에서 네트워크를 이용해 클라이언트에게 전달하려면 많은 트래픽이 발생할 것이다. 만약 한 페이지에서 작은 이미지를 여러 개 사용한다면 <u>Data URI Scheme</u>을 이용하는 방법이 더 나은 경우도 있다. 이미지를 Base64로 인코딩하여 문자열 형태로 소스코드에 넣는 형식을 말한다. <u>**url-loader**</u>는 이런 처리를 자동화해주는 로더이다.

url-loader 설치

```bash
$ npm i -D url-loader
```

웹팩 설정에 로더 추가

```javascript
{
  test: /\.png$/,
  use: {
    loader: 'url-loader', // url 로더를 설정한다
    options: {
      publicPath: './dist/', // file-loader와 동일
      name: '[name].[ext]?[hash]', // file-loader와 동일
      limit: 5000 // 5kb 미만 파일만 data url로 처리
    }
  }
}
```

설정 방법은  `limit` 속성을 제외하고 file-loader와 매우 유사하다. `limit` 속성은 모듈로 사용한 파일 중 크기가 `5kb` 미만인 파일만 url-loader를 적용하는 설정으로 만약 이 보다 크면 file-loader가 처리하는데 옵션 중 **<u>fallback</u>** 기본값이 file-loader이기 때문이다.

<br>

### babel-loader

**<u>babel-loader</u>**는 ES6에서 ES5로 변환할 때 사용할 수 있는데, `test`에는 ES6으로 작성한 자바스크립트 파일을 지정하고 `use`에 이를 변환할 때 바벨 로더를 설정한다.

babel-loader 설치

```bash
$ npm i -D babel-loader babel-core babel-preset-env
```

웹팩 설정에 로더 추가

```javascript
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: "node_modules",
        use: {
          loader: "babel-loader",
          options: {
            presets: ["env"],
          },
        },
      },
    ],
  },
}
```

`node_mudules` 폴더는 패키지 폴더로 제외해야하기 때문에 `exclude`에 설정되었다. 다음에 babel-loader에 대해 한번 더 공부해볼 예정이다.

<br>

<br>

## 참조

* [프론트엔드 개발환경의 이해: 웹팩(기본) - 김정한](https://jeonghwan-kim.github.io/series/2019/12/10/frontend-dev-env-webpack-basic.html)
  * 정말 잘 설명되어 있고, 기회가 된다면 나도 누군가에게 잘 설명할 수 있는 글을 써보고 싶다.
* [웹팩 핸드북 - Captain Pangyo]([https://joshua1988.github.io/webpack-guide/advanced/mode-config.html#%EC%9B%B9%ED%8C%A9-%EC%8B%A4%ED%96%89-%EB%AA%A8%EB%93%9C-mode](https://joshua1988.github.io/webpack-guide/advanced/mode-config.html#웹팩-실행-모드-mode))

