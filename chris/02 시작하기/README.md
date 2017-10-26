# 시작하기

## Vue.js란?

Vue(view와 발음이 같다)는 사용자 인터페이스를 만들기 위한 **_진보적인 프레임워크_** 이다.

[다른 프레임워크와의 비교](https://kr.vuejs.org/v2/guide/comparison.html)

MVVM(Model-View-ViewModel) 패턴에서 ViewModel을 담당한다.

## 시작하기

공식 가이드는 HTML, CSS, JavaScript에 대한 중간 수준의 지식을 전제로 한다.

CDN에서 import

> index.html

```html
<script src="https://unpkg.com/vue"></script>
```

## 선언적 렌더링

간단한 템플릿 구문을 이용해 선언적으로 DOM에 데이터를 렌더링 한다.

> app1.html

```html
<div id="app">
  {{ message }}
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      message: '안녕하세요 Vue!'
    }
  })
</script>
```

데이터와 DOM이 연결되어 모든것이 **_반응형_**이다.

브라우저 콘솔에서 아래 코드를 실행해보자.

> console

```js
app.message = '반응형 Vue!';
```

메세지가 다시 렌더링 되는 것을 볼 수 있다.

텍스트 보간법 외에 다음과 같이 엘리먼트 속성을 바인딩 할 수 있다.

> app2.html

```html
<div id="app-2">
  <span v-bind:title="message">
    내 위에 잠시 마우스를 올리면 동적으로 바인딩 된 title을 볼 수 있습니다!
  </span>
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  const app = new Vue({
    el: '#app-2',
    data: {
      message: '이 페이지는 ' + new Date() + ' 에 로드 되었습니다'
    }
  })
</script>
```

`v-bind` 속성을 디렉티브(Directive) 라고 한다.

디렉티브는 Vue에서 제공하는 특수 속성임을 나타내는 `v-` 접두어가 붙어있어 렌더링 된 DOM에 특수한 반응형 동작을 한다.

이 요소의 `title` 속성을 Vue 인스턴스의 `message` 속성으로 최신 상태를 유지한다.

브라우저 콘솔에서 다시 아래 코드를 실행하면 `title` 속성이 업데이트 된 것을 확인 할 수 있다.

> console

```js
app.message = '다시 렌더링';
```

## 조건문과 반복문

아래는 `v-if` 속성을 통해 엘리먼트의 존재 여부를 토글하는 방법을 나타낸다.

> app3.html

```html
<div id="app-3">
  <p v-if="seen">이제 나를 볼 수 있어요.</p>>
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  const app = new Vue({
    el: '#app-3',
    data: {
      seen: true
    }
  })
</script>
```

`app.seen = false` 를 콘솔에 입력하면 메세지가 사라진다.

이 예제는 텍스트와 속성 뿐 아니라 DOM의 **_구조_**에도 데이터를 바인딩 할 수 있음을 보여준다.

`v-for` 디렉티브는 배열 데이터를 사용해 항목 목록을 표시하는데 사용할 수 있다.

> app4.html

```html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  const app = new Vue({
    el: '#app-4',
    data: {
      todos: [{
          text: 'JavaScript 배우기'
        },
        {
          text: 'Vue 배우기'
        },
        {
          text: '무언가 멋진 것을 만들기'
        }
      ]
    }
  })
</script>
```

콘솔에서 `app.todos.push({ text: 'New Item' })` 를 입력하면 목록에 새 항목이 추가된다.

## 사용자 입력 핸들링

`v-on` 디렉티브를 사용하여 Vue 인스턴스에 메소드를 호출하는 이벤트 리스너를 등록 할 수 있다.

> app5.html

```html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">메시지 뒤집기</button>
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  const app = new Vue({
    el: '#app-5',
    data: {
      message: '안녕하세요! Vue.js!'
    },
    methods: {
      reverseMessage: function () {
        this.message = this.message.split('').reverse().join('');
      }
    }
  })
</script>
```

이 메소드는 DOM을 건드리지 않고 앱의 상태를 업데이트한다.

`v-model` 디렉티브는 form입력과 앱 상태를 양방향으로 바인딩 할 수 있다.

> app6.html

```html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  const app = new Vue({
    el: '#app-6',
    data: {
      message: '안녕하세요! Vue.js!'
    }
  })
</script>
```

## 컴포넌트를 사용한 작성방법

컴포넌트는 Vue의 또다른 중요한 개념이다.

컴포넌트를 트리 형태로 추상화하여 대규모 프로그램을 구축할 수 있도록 해준다.

![Component](https://kr.vuejs.org/images/components.png)

Vue에서, 컴포넌트는 본질적으로 미리 정의된 옵션을 가진 Vue 인스턴스이다.

아래는 Vue에서 컴포넌트를 등록하는 방법이다.

```js
Vue.component('todo-item', {
  template: '<li>할일 항목 하나입니다.</li>'
})
```

이렇게 선언하면 다른 컴포넌트의 템플릿에서 이 컴포넌트를 사용 할 수 있다.

```html
<ol>
  <todo-item/>
</ol>
```

하지만 이것은 모든 할 일 인스턴스에 똑같은 내용을 렌더링 한다.

부모 영역의 데이터를 자식 컴포넌트에 주입 할 수 있어야 한다.

`prop` 을 전달 받을 수 있도록 위 컴포넌트를 수정하자.

```js
Vue.compenent('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

이제 이 todo를 `v-bind` 를 사용하여 각각의 반복되는 컴포넌트에 전달할 수 있다.

> app7.html

```html
<div id="app-7">
  <ol>
    <todo-item v-for="item in groceryList" v-bind:todo="item" v-bind:key="item.id" />
  </ol>
</div>
<script src="https://unpkg.com/vue"></script>
<script>
  Vue.component('todo-item', {
    props: ['todo'],
    template: '<li>{{ todo.text }}</li>'
  })
  const app = new Vue({
    el: '#app-7',
    data: {
      groceryList: [{
          id: 0,
          text: 'Vegetables'
        },
        {
          id: 1,
          text: 'Cheese'
        },
        {
          id: 2,
          text: 'Whatever else humans are supposed to eat'
        }
      ]
    }
  })
</script>
```

컴포넌트는 나중에 더 다룰 것이다.

나중에 보게 될 컴포넌트를 사용한 앱의 모습은 아래와 같을 것이다.

```html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

