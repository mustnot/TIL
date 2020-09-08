# React Todo App with Typescript

> ../Django/DRF-React-Todo.md 와 이어지는 내용으로 React와 Typescript로 어떻게 프론트 작업을 했는지에 대해 작성했다. 모든 코드를 정리하는 것보다 코드 중 기억했으면 좋을 것 같은 코드들만 정리했다.

<br>

## Interface

> interface 명명 규칙 찾아봐야겠다.

**인터페이스(interface)**라는 개념을 typescript에서 처음 알았는데, 이미 Java나 C# 등 정적 타입 언어에서는 이미 많이 쓰이는 개념이라고 한다. 인터페이스는 기존 Javascript 내 클래스에서 구현부가 빠져 나와 객체에 프로퍼티 혹은 메서드가 어떠한 타입을 가지고 있는지 명시하여 선언하는 곳으로 보면 좋을 것 같다.

만들고자하는 Todo App에는 간단하게 살펴보면 하나의 인터페이스만 있다고 봐도 무방한데, 나는 여기서 조금 더 분리해서 작성하는 것이 좋다고 보여서 `Task`와 `AppState` 두 개로 분리했다. 

```typescript
interface Task {
  id: number | null;
  title: string;
  completed: boolean
}

interface AppState {
  todoList: taskState[];
  activeItem: taskState;
  editing: boolean;
}
```

먼저 `Task`를 살펴보면 다음과 같다.

* `id`: 할 일의 고유 `id` 값으로 보면 될 것 같다. 초기에는 설정하지 않기 때문에 `null` 값도 허용하기 위해 `number | null`로 선언하였다.
* `title`: 할 일 제목으로 문자열이 들어가기 때문에 `string`으로 선언
* `completed`: 완료 여부로 할 일이 완료했는지 체크하기 위함으로 `boolean` 선언

이렇게 작성된 `Task`를 이용해 `AppState`를 생성했다. `AppState`는 Todo App 내부에서 사용되는 `state`의 인터페이스로 아래와 같이 구성된다.

* `todoList`: 저장된 할 일이 목록화되어 저장되는데, 배열로 저장되는 점으로 `[]`로만 설정할 수 있지만, 배열 안에 들어가는 `object` 들이 `task` 와 같은 형태이기 때문에 `Task[]`로 선언되었다.
  (처음에 이렇게 하는 방법을 모르고 길게 작성했는데, 나중에 참고하면 될 것 같다)
* `activeItem`: 현재 활성화된 할 일로 보면 되어서 `Task`와 동일
* `editing`: 수정 여부를 판단하기 위해 `boolean`으로 선언

<br>

## Component

```typescript
class App extends React.Component<AppProps, AppState> {
  constructor() {}
  getCookie() {}
	componentWillMount() {}
	fetchTasks() {}
	handleChange() {}
	handleSubmit() {}
	handleDelete() {}
	startEdit() {}
	strikeUnstrike() {}
	render() {}
}
```

**Component** 안에는 `constructor(), render()` 포함 총 10개의 메서드로 구성되어 있다. 각각은 이름 그대로 행동(?)하며 메서드 간 호출로 상태가 변경된다. 각각 하나씩 살펴보자. 먼저 `render()` 먼저 살펴본다.

<br>

### render()

```typescript
render() {
  let tasks = this.state.todoList;
  let self = this;

  return (
    <div className="container">
      <div id="task-container">
        <div id="form-wrapper">
          <form onSubmit={this.handleSubmit} id="form">
            <div className="flex-wrapper">
              <div style={{ flex: 6 }}>
                <input
                  onChange={this.handleChange}
                  className="form-control"
                  id="title"
                  value={this.state.activeItem.title}
                  type="text"
                  name="title"
                  placeholder="Add task.."
                />
              </div>
              <div style={{ flex: 1 }}>
                <input id="submit" className="btn btn-primary" type="submit" name="Add..." />
              </div>
            </div>
          </form>
        </div>

        <div id="list-wrapper">
          {tasks.map(function (task, index) {
            return (
              <div key={index} className="task-wrapper flex-wrapper">
                <div onClick={() => self.strikeUnstrike(task)} style={{ flex: 7 }}>
                  {task.completed === false ? <span>{task.title}</span> : <del>{task.title}</del>}
                </div>
                <div style={{ flex: 1 }}>
                  <button onClick={() => self.startEdit(task)} className="btn btn-sm btn-outline-info">
                    Edit
                  </button>
                </div>
                <div style={{ flex: 1 }}>
                  <button onClick={() => self.handleDelete(task)} className="btn btn-sm btn-outline-dark delete">
                    Delete
                  </button>
                </div>
              </div>
            );
          })}
        </div>
      </div>
    </div>
  );
}
```

코드만 보더라도 굉장히 긴데, 결국 만들고자 하는 Todo App의 전체 모양이라고 생각하면 된다. 구조는 찬찬히 살펴보고 위에서부터 코드를 하나씩 뜯어보자. 우선 `render()`의 역할은 말 그대로 화면을 렌더링하는 함수로 보면 된다. 여기에 작성한 코드들을 통해 화면에 `App Component`를 렌더링한다.

먼저 변수 선언 부분이다.

* `let tasks = this.state.todoList;`: `tasks`는 할 일 목록으로 현재 `state object`의 `todoList`를 의미한다.
* `let self = this;`: 이 부분은 자세하게 알아봐야할 것 같아 Stackoverflow를 참조했다. 내부에서 호출되는 함수의 경우 그 컨텍스트를 찾기 위해서 참조를 하는데, 만약 `self = this`라고 설정하지 않고 호출하게 되면 중간에 변경되거나 한다면, 특정 값을 찾지 못하는 현상이 있을 수 있다. 이런 경우를 방지하기 위해 선언하여 사용한다. (맞는지 모르겠다)

아래는 주로 `html` 이 주를 이루는데, 기능 하나하나 살펴보면서 다시 보도록 하자.

<br>

### componentWillMount()

> flask의 after_request와 동일한 역할을 한다고 생각된다. 

`componentWillMount()`는 **LifeCycle API** 중 하나로 Component의 `cunstructor`가 완료되면 다음에 실행되는 메서드로 이 작업 이후에 `render()`가 호출되어 화면의 변화 혹은 데이터의 변화가 있을 경우 클라이언트에게 보여주는 역할을 한다. 어떻게 보면 `constructor`와 `render` 의 중간 다리 역할이라고 보면 될 것 같다.

여기서는 완료된 시점에 `fetchTasks()`라는 메서드를 호출하여 백엔드에서 지속적으로 할 일 리스트를 가져오는데 사용된다. 

**LifeCycle API**에는 `componetWillMount()` 외에도 다양한 메서드가 존재하는데, 아래 글을 참조하자.

* [React-Component | Reactjs Korea](https://ko.reactjs.org/docs/react-component.html)

<br>

그럼 코드를 보면 다음과 같이 사용하고 있다.

```typescript
componentWillMount() {
  this.fetchTasks();
}

fetchTasks() {
  fetch("http://127.0.0.1:8000/api/tasks")
  	.then((reponse) => response.json())
  	.then((data) => 
         this.setState({
			     todoList: data,
				 })
    )}
}
```

순서를 기억하자. `constructor() > componentWillMount() > render()` 순으로 실행되는데, 여기서 `componentWillMount()`는 `render()`전에 기존에 등록했던 할 일 목록을 가져오는 기능을 수행한다. 생각하면 아주 간단한데, Todo App에서 할 일 목록을 가져와야지 `render()`를 통해 클라이언트에게 보여줄 수 있기 않나라고 생각하면 그 역할을 수행하는 것이 `render()`라고 할 수 있다.

> 아직 React를 많이 사용하진 않았지만, 이 부분은 많이 사용하지 않을까 생각된다.

우리가 웹페이지를 만들다보면 백엔드에 API 호출을 통해 데이터를 받아오는 경우가 많은데, 주로 비동기 호출 중 하나인 AJAX를 많이 사용한다. 그럼 React에서는 AJAX 호출을 어떻게할까 살펴보면 여기서는 브라우저에 내장된 **window.fetch**를 이용한다.

문법 :

```javascript
fetch(url, {options})
// options에는 일반적으로 많이 사용되는 method, headers, body 등이 사용된다.
```

여기서는 `GET: http://127.0.0.1:8000/api/tasks`를 통해 등록된 할일 리스트를 가져왔다.

> ℹ️ 컴포넌트 생명주기 중에 어디에서 AJAX 호출을 할 수 있나요? 
>
> 공식 문서의 [AJAX와 APIs](https://ko.reactjs.org/docs/faq-ajax.html)의 위와 같은 내용이 있는데, 문서에 의하면 생명주기(LifeCycle) 메서드 중 `componentDidMount` 안에 추가되어야한다고 한다. 이는 데이터를 받아 올 때 `setState`를 통하여 컴포넌트를 업데이트 하기 위함이라고 되어 있다. (음 여기서는 `componentWillMount`에 사용했는데 무슨 차이가 있을까?)

예시 :

```react
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      error: null,
      isLoaded: false,
      items: []
    };
  }

  componentDidMount() {
    fetch("https://api.example.com/items")
      .then(res => res.json())
      .then(
        (result) => {
          this.setState({
            isLoaded: true,
            items: result.items
          });
        },
        // 주의: 컴포넌트의 실제 버그에서 발생하는 예외사항들을 넘기지 않도록 
        // 에러를 catch() 블록(block)에서 처리하기보다는 
        // 이 부분에서 처리하는 것이 중요합니다.
        (error) => {
          this.setState({
            isLoaded: true,
            error
          });
        }
      )
  }

  render() {
    // ...
  }
}
```

> 확실히 공식 문서에는 좋은 코드와 설명이 많은 것 같다. 참고하자

<br>

### handleSubmit()

```react
handleSubmit(e: any) {
    e.preventDefault();
    const csrftoken = this.getCookie("csrftoken");

    let url: string = "http://127.0.0.1:8000/api/tasks/";
    let method: string = "POST";

    fetch(url, {
      method: method,
      headers: {
        "Content-type": "application/json",
        "X-CSRFToken": csrftoken,
      },
      body: JSON.stringify(this.state.activeItem),
    })
      .then((response) => {
        this.fetchTasks();
        this.setState({
          activeItem: {
            id: null,
            title: "",
            completed: false,
          },
        });
      })
      .catch(function (error) {
        console.log("Error Message:", error);
      });
  }
```

> 코드를 조금 지웠는데, 그 부분은 나중에 보자.

먼저 코드를 살펴보면, 가장 먼저 파라미터로 `e`를 받도록 되어 있는데, `e`에는 무엇이 오는지 살펴보자. `e`는 **event**의 앞자를 딴 단어로 사용자가 특성 이벤트를 실행했을 때, 실행되는 함수라 `e`를 파라미터로 받도록 되어 있다.

> ℹ️ e.preventDefault()
>
> `html`에서 `a` 태그나 `submit` 태그는 고유의 동작이 있는데, 주로 페이지를 이동시킨다거나 `form` 안에 있는 `input` 등을 전송한다던가 하는 어떠한 동작이 있는데, 그 동작을 중단시키는 코드이다.

바로 `fetch` 부분으로 가면 먼저 `url`은 앞과 동일한 주소에 메소드만 `POST`만 바뀌었다. API를 만드는 방법은 여러가지가 있겠지만, 여기서는 `POST` 메소드 요청시 데이터가 추가된다.

기억해야할 포인트는 다음과 같다.

1. `POST` 요청시 `header`에 CSRF Token 포함하여 보내기
2. `headers`에 `Content-type` 명시하기
3. `body`는 `Content-type`에 맞춰 보내기

<br>

## Comment

사실 첫 웹을 만들었는데, 의외로 재미가 있었다. `html` 만을 사용해서 만든 사이트가 아니라, Python Class 같이 코드 짜듯이 만들어서 그런지 좋았는데, 한번 쯤 나만의 사이트를 만들어보는 것도 좋을 것 같다. 다음에는 블로그를 만들어봐도 좋을 것 같다. (도전!)