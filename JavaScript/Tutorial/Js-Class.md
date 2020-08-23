# Javascript - Class

>  ℹ️ [모던 Javascript 튜토리얼](https://ko.javascript.info/)을 통해 공부한 내용으로 Part 1의 클래스(Class)에 대해 공부하며 정리한 글입니다.

<br>

## 클래스와 기본 문법

> 클래스는 객체 지향 프로그래밍에서 특정 객체를 생성하기 위해 변수와 메소드를 정의하는 일종의 틀로, 객체를 정의하기 위한 상태(멤버 변수)와 메서드(함수)로 구성된다. - 위키백과

실무에서는 사용자나 물건 같이 동일한 종류의 객체를 여러 개 생성해야 하는 경우가 잦은데, 이럴 때 `new` 연산자와 생성자 함수에서 배운 `new function`을 사용할 수 있다. 여기에 모던 자바스크립트에는 `클래스(class)` 라는 문법을 사용하면 객체 지향 프로그래밍에서 사용되는 다양한 기능을 자바스크립트에서 사용 가능하다.

### 기본 문법

```javascript
class MyClass {
    // 여러 메서드를 정의할 수 있음
    constructor() {...}
    method1() {...}
    method2() {...}
    method3() {...}
    ...
}
```

이렇게 클래스를 정의하고, `new MyClass()`를 호출하면 내부에서 정의한 메서드가 들어 있는 객체가 생성된다. 객체의 기본 상태를 설정해주는 생성자 메서드 `constructor()`는 `new`에 의해 자동으로 호출되므로, 특별한 절차 없이 객체를 초기화 할 수 있다.

예시 :

```javascript
class User {
    constructor(name) {
        this.name = name;
    }
    sayHi() {
        alert( `Hello! ${this.name}` );
    }
}

let user = new User("John");
user.sayHi();
```

<br>

### 클래스 표현식

함수처럼 클래스도 다른 표현식 내부에서 정의, 전달, 반환, 할당할 수 있다.

```javascript
let User = class {
    sayHi() {
        alert("Hello")
    }
};
```

또한 <u>필요에 따라</u> 클래스는 동적으로 생성하는 것도 가능하다.

```javascript
function makeClass(phrase) {
    return class {
        sayHi() {
            alert( phrase );
        };
    };
}

let User = makeClass("Hello");
new User().sayHi();
```

<br>

### getter와 setter

리터럴을 사용해 만든 객체처럼 클래스도 `getter`와 `setter`, 계산된 프로퍼티(computed property)를 포함할 수 있다.

예시 :

```javascript
class User {
    constructor(name) {
        this.name = name
    }
    get name() {
        return this._name;
    }
    set name(value) {
		if (value.length < 4) {
            alert("이름이 너무 짧습니다.")
            return;
        }
        this._name = value;
    }
}

let user = new User("John");
console.log(user.name);
user = new User("");
```

이러한 방법으로 클래스 선언시 `User.prototype`에 `getter`와 `setter`가 만들어지므로 `get, set`을 사용할 수 있다.

<br>

### 계산된 메서드 이름 [...]

대괄호 `[...]` 를 이용해 계산된 메서드 이름 (computed method name)을 만드는 예시를 보자.

예시 :

```javascript
class User {
    ['say' + 'Hi'] {
        alert("Hello");
    }
}

new User().sayHi();
```

<br>

### 클래스 필드

> **⚠️ 구식 브라우저에선 폴리필이 필요할 수 있다.**
>
> 클래스 필드는 근래에 더해진 기능이다.

지금까지 살펴본 예시는 메서드가 하나만 있었는데, `클래스 필드(class field)` 라는 문법을 사용하면 어떤 종류의 프로퍼티도 클래스에 추가할 수 있다.

예시 :

```javascript
class User {
    name = "John";
	sayHi() {
        alert(`Hello, ${this.name}!`);
    }
}
```

<br>

### 과제 : 클래스로 다시 작성하기

```javascript
// before
function Clock({ template }) {
    let timer;
    function render() {
        let date = new Date();

        let hours = date.getHours();
        if (hours < 10) hours = '0' + hours;

        let mins = date.getMinutes();
        if (mins < 10) mins = '0' + mins;

        let secs = date.getSeconds();
        if (secs < 10) secs = '0' + secs;

        let output = template
        .replace('h', hours)
        .replace('m', mins)
        .replace('s', secs);

        console.log(output);
    }

    this.stop = function() {
        clearInterval(timer);

    this.start = function() {
        render();
        timer = setInterval(render, 1000);
    }
};

let clock = new Clock({template: 'h:m:s'});
clock.start();
```

```javascript
// after
class Clock {
    constructor(template) {
        this.template = template
    }

    render() {
        let date = new Date();

        let hours = date.getHours();
        if (hours < 10) hours = '0' + hours;

        let mins = date.getMinutes();
        if (mins < 10) mins = '0' + mins;

        let secs = date.getSeconds();
        if (secs < 10) secs = '0' + secs;

        let output = `${hours}:${mins}:${secs}`

        console.log(output);
    }

    stop() {
        clearInterval(this.timer);
    }
    
    start() {
        this.render();
        this.timer = setInterval(this.render, 1000);
    }
};

let clock = new Clock("h:m:s");
clock.start();
```

<br>

<br>

## 클래스 상속

클래스 상속을 사용하면 클래스를 다른 클래스로 확장할 수 있다. 기존에 존재하던 기능을 토대로 새로운 기능을 만들 수 있다.

### `extends` 키워드

예시 : `Animal` 클래스 

```javascript
class Animal {
    constructor(name) {
        this.speed = 0;
        this.name = name;
    }
    run(speed) {
        this.speed = speed;
        console.log(`${this.name} 은(는) 속도 ${this.speed}로 달립니다.`);
    }
    stop() {
        this.speed = 0;
        console.log(`${this.name} 이(가) 멈췄습니다.`);
    }
}

let animal = new Animal("동물")
```

또 다른 클래스인 `Rabbit`을 만들어보자.

```javascript
class Rabbit extends Animal {
    hide() {
        this.speed = 0;	// 내가 추가해봄, 숨었다면 속도가 0가 될 것 같아서
        console.log(`${this.name} 이(가) 숨었습니다!`);
    }
}

let rabbit = new Rabbit("산토끼");
rabbit.run(5);
rabbit.hide();
```

이런 식으로 `Rabbit`이라는 클래스는 `Animal` 클래스를 상속받아 `Animal` 클래스에 정의했던 메서드에 접근할 수 있다. 다만 반대로 `Animal`에서는 `Rabbit`에서 정의한 메서드는 `hide()`는 사용할 수 없다.

> **ℹ️ `extends` 뒤에 표현식이 올 수도 있습니다.**
>
> 클래스 문법은 `extends` 뒤에 표현식이 와도 처리해준다.
>
> ```javascript
> function f(phrase) {
>     return class {
>         sayHi() { alert(phrase); }
>     }
> }
> class User extends f("Hello") {};
> new User().sayHi();
> ```

<br>

### 메서드 오버라이딩

특별한 사항이 없다면, `class Rabbit`은 `class Animal`에 있는 메서드를 '그대로' 상속받는다. 그런데 `Rabbit`에서 `stop()` 등의 메서드를 자체적으로 정의하면, 상속받은 메서드가 아닌 자체 메서드가 사용된다.

```javascript
class Rabbit extends Animal {
    stop() {...}
}
```

개발을 하다보면 부모 메서드 전체를 교체하지 않고, 부모 메서드를 토대로 일부 기능만 변경하고 싶을 때가 생긴다. 혹은 부모 메서드의 기능을 확장하고 싶을 때도 있다. 이럴 때 커스텀 메서드를 만들어 작업하게 되는데, 커스텀 메서드를 만들었다 치더라도 이 과정 전/후에는 부모 메서드를 호출하고 싶을 때가 있다. (딜레마 같다)

이럴 때 사용할 수 있는 키워드로 `super`가 있다.

* `super.methods(...)` 는 부모 클래스에 정의된 메서드를 호출한다.
* `super(...)`는 부모 생성자를 호출하는데, 자식 생성자 내부에서만 사용할 수 있다.

이런 특징을 이용해 토끼가 멈추면, 자동으로 숨도록 하는 코드를 만들어보자.

```javascript
class Animal {
    constructor(name) {
        this.speed = 0;
        this.name = name;
    }
    run(speed) {
        this.speed = speed;
        console.log(`${this.name} 은(는) 속도 ${this.speed}로 달립니다.`);
    }
    stop() {
        this.speed = 0;
        console.log(`${this.name} 이(가) 멈췄습니다.`);
    }
}

class Rabbit extends Animal {
    hide() {
        this.speed = 0;	// 내가 추가해봄, 숨었다면 속도가 0가 될 것 같아서
        console.log(`${this.name} 이(가) 숨었습니다!`);
    }
    
    stop() {
        super.stop();
        this.hide();
    }
}

let rabbit = new Rabbit("흰 토끼");

rabbit.run(5);
rabbit.stop();
```

> ℹ️ **화살표 함수엔 `super`가 없다.**
>
> [화살표 함수 다시 살펴보기](https://ko.javascript.info/arrow-functions)에서 살펴본 바와 같이, 화살표 함수는 `super`를 지원하지 않습니다.
>
> `super`에 접근하면 아래 예시와 같이 `super`를 외부 함수에서 가져옵니다.
>
> ```javascript
> class Rabbit extends Animal {
>   stop() {
>     setTimeout(() => super.stop(), 1000); // 1초 후에 부모 stop을 호출합니다.
>   }
> }
> ```
>
> 화살표 함수의 `super`는 `stop()`의 `super`와 같아서 위 예시는 의도한 대로 동작합니다. 그렇지만 `setTimeout`안에서 ‘일반’ 함수를 사용했다면 에러가 발생했을 겁니다.
>
> ```javascript
> // Unexpected super
> setTimeout(function() { super.stop() }, 1000);
> ```

<br>

### 생성자 오버라이딩

생성자 오버라디잉은 메서드 오버라이딩과 달리 좀 더 까다로운데, 예시를 살펴보면 `Rabbit` 자체에는 `constructor`가 없다는 것을 볼 수 있다. 명세서에 따르면 클래스가 다른 클래스를 상속받고 `constructor`가 없는 경우에는 아래처럼 '비어있는' `constructor`가 만들어진다.

```javascript
class Rabbit extends Animal {
    constructor(...args) {
        super(...args)
    }
}
```

보이듯이 생성자는 기본적으로 부모 `constructor`를 호출하고, 이때 부모 `constructor`에도 인수를 모두 전달한다. 클래스 자체 생성자가 없는 경우엔 이런 일이 모두 자동으로 일어난다. 

예시 : `Rabbit`에 커스텀 생성자 추가

```javascript
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  // ...
}

class Rabbit extends Animal {

  constructor(name, earLength) {
    this.speed = 0;
    this.name = name;
    this.earLength = earLength;
  }

  // ...
}

// 동작하지 않습니다!
let rabbit = new Rabbit("흰 토끼", 10);
```

```javascript
// 에러 메세지
ReferenceError : Must call super constructor in derived class before accessing 'this' or returning from derived constructor
```

예시처럼 커스텀 생성자를 생성하면 위와 같은 에러 메세지가 나타나는데, 해석하면 다음과 같다.

* 상속 클래스의 생성자에선 반도시 `super(...)`를 호출해야하는데, `super(...)`를 호출하지 않아 에러가 발생했고, `super(...)`는 `this`를 사용하기 전에 반드시 호출해야한다.

그렇다면 왜 `super(...)`를 호출해야하는 걸까? 자바스크립트는 '상속 클래스의 생성자 함수(derived constructor)'와 그렇지 않은 생성자 함수를 구분한다. 상속 클래스의 생성자 함수엔 특수 내부 프로퍼티인 `[[ConstructorKind]]: "derived"` 가 이름표처럼 붙는다.

일반 클래스의 생성자 함수와 상속 클래스의 생성자 함수 간 차이는 `new`와 함께 드러난다.

*  일반 클래스가  `new`와 함께 실행되면, 빈 개겣가 만들어지고 `this`에 이 객체를 할당한다.
* 반면, 상속 클래스의 생성자 함수가 실행되면, 일반 클래스에서 일어난 일이 일어나지 않고 상속 클래스의 생성자 함수는 빈 객체를 만들고 `this`에 이 객체를 할당하는 일을 부모 클래스의 생성자가 처리해주길 기대한다.

이런 차이로 인해 상속 클래스의 생성자에선 `super`를 호출하여 부모 생성자를 먼저 실행해주어야한다.

```javascript
class Animal {

  constructor(name) {
    this.speed = 0;
    this.name = name;
  }

  // ...
}

class Rabbit extends Animal {

  constructor(name, earLength) {
    super(name);	// super를 먼저 호출하여 부모 클래스를 먼저 생성
    this.earLength = earLength;
  }

  // ...
}

// 이제 에러 없이 동작합니다.
let rabbit = new Rabbit("흰 토끼", 10);
alert(rabbit.name); // 흰 토끼
alert(rabbit.earLength); // 10
```

<br>

<br>

## 정적 메서드와 정적 프로퍼티

`"prototype"`이 아닌 클래스 함수 자체에 메서드를 설정할 수도 있습니다. 이런 메서드를 정적(static) 메서드라고 부르는데, 정적 메서드는 아래와 같이 클래스 안에 `static` 키둬를 붙여 만들 수 있다.

```javascript
class User {
    static staticMethod() {
        alert(this == User);
    }
}
User.staticMethod();	// true
```

정적 메서드는 메서드를 프로퍼티 형태로 직접 할당하는 것과 동일한 일을 한다.

```javascript
class User { }
User.staticMethod = function() {
    alert(this == User);
};
User.staticMethod();	// true
```

`User.staticMethod()`가 호출될 때 `this`의 값을 클래스 생성자인 `User` 자체가 된다. 정적 메서드는 어떤 특정한 객체가 아닌 클래스에 속한 함수를 구현하고자 할 때 주로 사용된다. 

예시 : 객체 `Article`이 여러 개 있고, 이들을 비교해줄 함수가 필요하다고 가정할 때, 가장 먼저 아래와 같이 `Article.compare`을 추가하는 방법이 떠오를 것이다.

```javascript
class Article {
  constructor(title, date) {
    this.title = title;
    this.date = date;
  }

  static compare(articleA, articleB) {
    return articleA.date - articleB.date;
  }
}

// 사용법
let articles = [
  new Article("HTML", new Date(2019, 2, 1)),
  new Article("CSS", new Date(2019, 1, 1)),
  new Article("JavaScript", new Date(2019, 11, 1))
];

articles.sort(Article.compare);

alert( articles[0].title ); // CSS
```

여기서 `Article.compare`는 article(글)을 비교해주는 수단으로, 글 전체를 '위에서' 바라보며 비교를 수행한다. `Article.compare`이 글 하나의 메서드가 아닌 클래스의 메서드여야 하는 이유가 여기에 있다.

다음 예시는 "팩토리" 메서드를 구현한 코드로 다양한 방법을 사용해 조건에 맞는 `article` 인스턴스를 만들어야한다고 가정하자

1. 매개변수(`title, date 등`)를 이용해 관련 정보가 담긴 `article` 생성
2. 오늘 날짜를 기반으로 비어있는 `article` 생성
3. 기타 등등

첫 번째 방법은 생성자를 사용해 구현할 수 있다. 두 번째 방법은 클래스에 정적 메서드를 만들어 구현할 수 있다.

예시 : `Article.createTodays()`

```javascript
class Article {
  constructor(title, date) {
    this.title = title;
    this.date = date;
  }

  static createTodays() {
    // this는 Article입니다.
    return new this("Today's digest", new Date());
  }
}

let article = Article.createTodays();

alert( article.title ); // Today's digest
```

위 같은 방식으로 `Today's digest`라는 글이 필요할 때마다 `Article.createTodays()`를 호출하면 된다. 여기서도 마찬가지로 `Article.createTodays()`는 `article` 메서드가 아닌 전체 클래스의 메서드이다. 정적 메서드는 아래 예시와 같이 항목 검색, 저장, 삭제 등을 수행해주는 데이터베이스 관련 클래스에도 사용된다.

```javascript
Article.remove({id: 12345});
```

<br>

### 정적 프로퍼티

> ⚠️ **최근에 추가**되어 Chrome에서만 동작 할 수 있습니다.

아래와 같이 일반 클래스 프로퍼티와 유사하게 생겼는데, 앞에 `static`이 붙는다는 점만 다르다.

```javascript
class Article {
    static publisher = "Ilya Kantor";
}
```

<br>

### 정적 프로퍼티와 메서드 상속

> 코드만 보고 넘어가자.

```javascript
class Animal {
  static planet = "지구";

  constructor(name, speed) {
    this.speed = speed;
    this.name = name;
  }

  run(speed = 0) {
    this.speed += speed;
    alert(`${this.name}가 속도 ${this.speed}로 달립니다.`);
  }

  static compare(animalA, animalB) {
    return animalA.speed - animalB.speed;
  }

}

// Animal을 상속받음
class Rabbit extends Animal {
  hide() {
    alert(`${this.name}가 숨었습니다!`);
  }
}

let rabbits = [
  new Rabbit("흰 토끼", 10),
  new Rabbit("검은 토끼", 5)
];

rabbits.sort(Rabbit.compare);

rabbits[0].run(); // 검은 토끼가 속도 5로 달립니다.

alert(Rabbit.planet); // 지구
```

<br>

<br>

## private, protected 프로퍼티와 메서드

객체 지향 프로그래밍에서 가장 중요한 원리 중 하나는 '내부 인터페이스와 외부 인터페이스를 구분 짓는 것'이다. 단순히 문자열을 출력하는 것이 아닌 복잡한 어플리케이션을 구현하려면, 내부 인터페이스와 외부 인터페이스를 구분하는 방법을 "반드시" 알아야한다. 

<br>

### 내부 인터페이스와 외부 인터페이스

객체 지향 프로그래밍에서 프로퍼티와 메서드는 두 그룹으로 분류된다.

* 내부 인터페이스 (internal interface) : 동일한 클래스 내의 다른 메서드에선 접근할 수 있지만, 클래스 밖에서 접근할 수 없는 프로퍼티와 메서드
* 외부 인터페이스 (external interface) : 클래스 밖에서도 접근 가능한 프로퍼티와 메서드

자바스크립트에는 아래와 같은 두 가지 타입의 객체 필드 (프로퍼티와 메서드)가 있습니다.

* public : 어디서든지 접근할 수 있으며 외부 인터페이스를 구성한다. 현재까지 다룬 모든 메서드와 프로퍼티는 public이다.
* private : 클래스 내부에서만 접근할 수 있으며 내부 인터페이스를 구성할 때 쓰인다.

자바스크립트 이외의 다수 언어에서 클래스 자신과 자손 클래스에서만 접근을 허용하는 'protected' 필드를 지원한다. protected 필드는 private와 비슷하지만, 자손 클래스에서도 접근이 가능하다는 점이 다르다. protected 필드도 내부 인터페이스를 만들 때 유용하다. 자손 클래스의 필드에 접근해야 하는 경우가 많기 때문에, protected 필드는 private 필드보다 조금 더 광범위하게 사용합니다.

<br>

### 프로퍼티 보호하기

예시 : 커피 머신 클래스

```javascript
class CoffeeMachine {
    waterAmount = 0;

	constructor(power) {
        this.power = power;
        console.log( `전력량이 ${power}인 커피머신을 만듭니다.` );
    }
}

let coffeeMachine = new CoffeeMachine(100);
conffeeMachine.waterAmount = 200;
```

현재 `waterAmount`와 `power`는 public으로 손쉽게 누구나 변경하기 쉬운 상태이다. 그렇다면 `waterAmount`를 protected로 바꿔서 `waterAmount`를 통제하며 0 미만의 값으로는 설정하지 못하도록 설정한다.

```javascript
class CoffeeMachine {
    _waterAmount = 0;	// waterAmount property to changed protected

	constructor(power) {
        this.power = power;
        console.log( `전력량이 ${power}인 커피머신을 만듭니다.` );
    }

	get waterAmount() {
        return this._waterAmount;
    }

	set waterAmount(value) {
        if (value < 0) {
            throw new Error( "물의 양은 음수가 될 수 없습니다." );
        }
        this._waterAmount = value;
    }
}
```

<br>

### 읽기 전용 프로퍼티

`power` 프로퍼티를 읽기만 가능하도록 만들어보자. 프로퍼티를 생성할 때에만 값을 할당할 수 있고, 그 이후에는 값을 절대로 수정하지 말아야 하는 경우가 있는데, 이럴 때 읽기 전용 프로퍼티를 활용할 수 있다.

```javascript
class CoffeeMachine {
    // ...
    constructor(power) {
        this._power = power;
    }
    
    get power() {
        return this._power;
    }
}

let coffeeMachine = new CoffeeMachine(100);
coffeeMachine.power = 25; // Error (setter 없음)
```

<br>

### private 프로퍼티

> **⚠️ 최근에 추가됨**
>
> 스펙에 추가된지 얼마 안 된 문법으로 지원하지 않거나 부분적으로만 지원하는 엔진을 사용한다면 폴리필을 구현해야 합니다. private 프로퍼티와 메서드는 제안(proposal) 목록에 등재된 문법으로, 명세서에 등재되기 직전 상태이다.

private 프로퍼티와 메서드는 `#` 으로 시작한다. `#`이 붙으면 클래스 안에서만 접근할 수 있다.

```javascript
class CoffeeMachine {
  #waterLimit = 200;

  #checkWater(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    if (value > this.#waterLimit) throw new Error("물이 용량을 초과합니다.");
  }

}

let coffeeMachine = new CoffeeMachine();

// 클래스 외부에서 private에 접근할 수 없음
coffeeMachine.#checkWater(); // Error
coffeeMachine.#waterLimit = 1000; // Error
```

```javascript
class CoffeeMachine {
    #waterLimit = 200;
    #waterAmount = 0;
    
    get waterAmount() {
        return this.#waterAmount;
    }
	set waterAmount(value) {
        if (value < 0) throw new Error( "물의 양은 음수가 될 수 없습니다." );
        this.#waterAmount = value;
    }
}

let machine = new CoffeeMachine();

machine.waterAmount = 100;
console.log(machine.waterAmount);	// 100, 만약 getter가 없다면 Error!
console.log(machine.#waterAmount);	// Error!
```

> ⚠️ **private 필드는 this[name]으로 접근하거나 사용할 수 없습니다.**
>
> private 필드는 특별합니다.
>
> 알다시피, 보통은 `this[name]`을 사용해 필드에 접근할 수 있습니다.
>
> ```javascript
> class User {
>   ...
>   sayHi() {
>     let fieldName = "name";
>     alert(`Hello, ${this[fieldName]}`);
>   }
> }
> ```
>
> 하지만 private 필드는 `this[name]`으로 접근할 수 없습니다. 이런 문법적 제약은 필드의 보안을 강화하기 위해 만들어졌습니다.

<br>

<br>

## 내장 클래스 확장하기

배열, 맵 같은 내장 클래스도 확장 가능합니다. 아래 예시는 `Array`를 상속받아 만들어진 클래스입니다.

예시 :

```javascript
class PowerArray extends Array {
    isEmpty() {
        return this.length == 0;
    }
}

let arr = new PowerArray(1, 2, 5, 10, 50);
alert(arr.isEmpty());	// false
```

<br>

<br>

## `instanceof`로 클래스 확인하기

`instanceof` 연산자를 사용하면 객체가 특정 클래스에 속하는지 아닌지를 확인할 수 있고 , 상속 관계도 확인해준다. 확인 기능은 다양한 곳에서 쓰이는데, 여기에서는 `instanceof`를 사용해 인수 타입에 따라 이를 다르게 처리하는 다형적인 함수를 만드는데 사용한다.

<br>

### instanceof 연산자

```javascript
obj instanceof Class
```

`obj`가 `Class`에 속하거나 `Class`에 속하거나 `Class`를 상속받는 클래스에 속하면 `true`가 반환된다.

예시 :

```javascript
class Rabbit {}
let rabbit = new Rabbit();

// rabbit이 클래스 Rabbit의 객체인가?
console.log( rabbit instanceof Rabbit );	// true
```

```javascript
let arr = [1, 2, 3];
console.log( arr instanceof Array );  // true
console.log( arr instanceof Object ); // true
```

위 예시에서 `arr`는 클래스 `Object`에도 속한다. `Array`는 프로토타입 기반으로 `Object`를 상속 받는다.

`instanceof` 연산자는 보통 프로토타입 체인을 거슬러 올라가며 인스턴스 여부나 상속 여부를 확인한다. 그런데 정적 메서드 `Symbol.hasInstance`를 사용하면 직접 확인 로직을 설정할 수도 있다.

`obj instanceof Class`는 대략 아래와 같은 알고리즘으로 동작한다.

1. 클래스에 정적 메서드 `Symbol.hasInstance`가 구현되어 있으면, `obj instanceof Class` 문이 실행될 때, `Class[Symbol.hasInstance](obj)`가 호출된다. 호출 결과는 `true`나 `false`이어야 한다. 이런 규칙을 기반으로 `instanceof`의 동작을 커스터마이징 할 수 있다.

    ```javascript
    // canEat 프로퍼티가 있으면 animal이라고 판단할 수 있도록
    // instanceof의 로직을 직접 설정
    class Animal {
        static [Symbol.hasInstance](obj) {
            if (obj.canEat) return true;
        }
    }
    
    let obj = { canEat: true };
    console.log( obj instanceof Animal );
    ```

2. 그런데, 대부분의 클래스엔 `Symbol.hasInstance`가 구현되어 있지 않다. 이럴 때엔 일반적인 로직이 사용된다. `obj instanceof Class`는 `Class.prototype`이 `obj` 프로토타입 체인 상의 프로토타입 중 하나와 일치하는지 확인한다. 차례 차례 비교를 진행한다.

    ```javascript
    obj.__proto__ === Class.prototype?
    obj.__proto__.__proto__ === Class.prototype?
    obj.__proto__.__proto__.__proto__ === Class.prototype?
    ...
    // 이 중 하나라도 true라면 true를 반환합니다.
    // 그렇지 않고 체인의 끝에 도달하면 false를 반환합니다.
    ```

    위 예시에서 `rabbit.__proto__ === Rabbit.prototype` 이 `true`이기 때문에 `instanceof`는 `true`를 반환한다. 상속받은 클래스를 사용하는 경우에 두 번째 단계에서 일치 여부가 확인된다.

    ```javascript
    class Animal {}
    class Rabbit extends Animal {}
    
    let rabbit = new Rabbit();
    console.log( rabbit instanceof Animal ); // true
    
    // rabbit.__proto__ === Rabbit.prototype // false
    // rabbit.__proto__.__proto__ === Animal.prototype // true
    ```

<br>

<br>

## 믹스인 (mixin)

> ℹ️ **믹스인 (mixin)** 은 다른 클래스를 상속 받을 필요 없이 이들 클래스에 구현되어 있는 메서드를 담고 있는 클래스라 정의한다. 다시 말해 특정 행동을 실행해주는 메서드를 제공하는데 단독으로 쓰이지 않고 다른 클래스에 행동을 더해주는 용도로 사용된다. - Wikipedia

자바스크립트는 단일상속만을 허용하는 언어입니다. 객체엔 단 하나의 `[[Prototype]]`만 있을 수 있고, 클래스는 클래스 하나만 상속받을 수 있습니다. 그런데 가끔 이런 제약이 한계처럼 느껴질 때가 있는데, 예를 들어 클래스 `StreetSweeper`와 클래스 `Bicycle`이 있는데, 이 둘을 섞어 `StreetSweepingBicycle`을 만들고 싶다고 하자. 또는 클래스  `User`와 이벤트를 생성해주는 코드가 담긴 클래스 `EventEmitter`가 있는데, `EventEmitter`의 기능을 `User`에 추가해 사용자가 이벤트를 열 수 있게 해주고 싶다고 생각해보자.

이럴 때 **<u>믹스인</u>**이라 불리는 개념을 사용하면 도움이된다.

<br>

### 믹스인 예시

자바스크립트에서 믹스인을 구현할 수 있는 가장 쉬운 방법은 유용한 메서드 여러 개가 담긴 객체를 하나 만드느느 것이다. 이렇게 하면 다수의 메서드를 원하는 클래스의 프로토타입에 쉽게 병합할 수 있다.

예시 : `sayHiMixin`을 이용해 `User`에게 '언어 능력'을 부여

```javascript
// mixin
let sayHiMixin = {
    sayHi() {
        alert(`Hello ${this.name}`);
    },
    sayBye() {
        alert(`Bye ${this.name}`);
    }
};

class User {
    constructor(name) {
        this.name = name;
    }
}
// 메서드 복사
Object.assign(User.prototype, sayHiMixin);
new User("Dude").sayHi(); // Hello Dude!
```

상속 없이 메서드만 복사하는 방식으로 믹스인을 활용하면, `User`가 아래 예시처럼 다른 클래스를 상속받는 동시에 믹스인에 구현된 추가 메서드도 사용할 수 있다.

```javascript
class User extends Person {
    //...
}
Objects.assign(User.prototype, sayHiMixin);
```

물론 믹스인 안에서 믹스인 상속을 사용하는 것도 가능하다.

```javascript
let sayMixin = {
    say(phrase) {
        alert(phrase);
    }
};

let sayHiMixin = {
    __proto__: sayMixin,
    sayHi() {
        super.say( `Hello, ${this.name}` );
    },
    sayBye() {
        super.say( `Bye, ${this.name}` );
    }
};

class User {
    constructor(name) {
        this.name = name;
    }
}

Object.assign(User.prototype, sayHiMixin);
new User("Dude").sayHi();
```

<br>

### 이벤트 믹스인

실제로 사용할 수 있는 믹스인을 만들어보자. 상당수 브라우저 객체는 이벤트 생성이라는 중요한 기능을 가지고 있다. 이벤트는 정보를 필요로 하는 곳에 '정보를 널리 알리는 (broadcast)' 훌륭한 수단이다. 아래 예시에서는 클래스나 객체에 이벤트 관련 함수를 쉽게 추가할 수 있도록 해주는 믹스인을 만들어 보겠습니다.

* 믹스인은 뭔가 중요한 일이 발생했을 때 '이벤트를 생성하는 메서드', `.trigger(name, [...data])` 를 제공한다. 인수  `name`은 이벤트 이름이고, 뒤따르는 선택 인수는 이벤트 데이터 정보를 담습니다.
* 메서드 `.on(name, handler)`은 `name`에 해당하는 이벤트에 리스터로 `handler` 함수를 추가하고 `.on()`은 이벤트(`name`)가 트리거 될 때 호출되고 `.trigger` 호출에서 인수를 얻습니다. (?!?)
* 메서드 `.off(name, handler)`는 `handler` 리스너를 제거한다.

믹스인을 추가하면, 사용자가 로그인할 때 객체 `user`가 `"login"` 이라는 이벤트를 생성할 수 있게 된다. 또 다른 객체 `calendar`는 `user`가 생성한 이벤트인 `"login"`을 듣고 사용자에 맞는 달력을 보여줄 수도 있다.

메뉴의 항목을 선택했을 때 객체 `menu`가 `"select"`라는 이벤트를 생성하고, 다른 객체는 `"select"`에 반응하는 이벤트 핸들러를 할당할 수도 있을 것이다. 이벤트 믹스인은 이러한 용도로 활용 가능하다.

> 📌 분명 설명만 듣기로는 굉장히 유용한 기능으로 보인다. 나중에 한번 더 봐서라도 이해할 수 있도록 해보자.

```javascript
let eventMixin = {
    /*
     * 이벤트 구독
     * 사용패턴 : menu.on('select', function(item) {...})
    */
    on(eventName, handler) {
        if (!this._eventHandlers) this._eventHandlers = {};
        if (!this._eventHandlers[eventName]) {
            this._eventHandlers[eventName] = [];
        }
        this._eventHandlers[eventName].push(handler);
    },
    
    /*
     * 이벤트 구독 취소
     * 사용패턴 : menu.off('select', handler)
    */
    off(eventName, handler) {
        let handlers = this._eventHandlers?.[eventName];
        if (!handlers) return;
        for (let i = 0; i < handlers.length; i++) {
            if (handlers[i] == handler) {
                handlers.splice(i--, 1);
            }
        }
    },
    
    /*
     * 주어진 이름과 데이터를 기반으로 이벤트 생성
     * 사용패턴 : this.trigger('select', data1, data2);
    */
    trigger(eventName, ...args) {
        if (!this._eventHandlers?.[eventName]) {
            return;
        }
        this._eventHandlers[eventName]
        .forEach(handler => handler.apply(this, args));
    }
};
```

* `.on(eventName, handler)` : `eventName`에 해당하는 이벤트가 발생하면 실행시킬 함수 `handler`를 할당한다. 한 이벤트에 대응하는 핸들러가 여러 개 있을 때, 프로퍼티 `_eventHandlers`는 핸들러가 담긴 배열을 저장한다. 여기서는 핸들러가 추가만 된다.
* `.off(eventName, handler)` : 핸들러 리스트에서 `handler`를 제거한다.
* `.trigger(eventName, ...args)` : 이벤트를 생성한다. `_eventHandlers[eventName]`에 있는 모든 핸들러가 `...args`와 함께 호출된다.

사용법 :

```javascript
class Menu {
    choose(value) {
        this.trigger("select", value);
    }
}

Object.assign(Menu.prototype, eventMixin);

let menu = new Menu();
menu.on("select", value => alert(`선택된 값: ${value}`));
menu.choose("123");
```

> 오타 주의