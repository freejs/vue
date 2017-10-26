# Vue 인스턴스

## Vue 인스턴스 만들기

모든 Vue 앱은 `Vue` 함수로 새 Vue 인스턴스를 만드는 것 부터 시작한다.

```js
var vm = new Vue({
  // 옵션
})
```

MVVM 패턴과 직접적인 관련은 없지만 Vue 디자인은 부분적으로 영감을 받았다.

Vue 인스턴스를 참조하기 위해 변수 `vm` (ViewModel의 약자)을 컨벤션으로 사용한다.

Vue 인스턴스를 인스턴스화 할 때는 데이터, 템플릿, 마운트할 엘리먼트, 메소드, 라이프사이클 콜백 등의 옵션을 포함하는 `options` 객체를 인자로 전달해야한다.

전체 옵션 목록은 [API reference](https://kr.vuejs.org/v2/api/)에서 찾을 수 있다.

`Vue` 생성자는 미리 정의 된 옵션으로 재사용 가능한 컴포넌트 생성자를 생성하도록 확장 될 수 있다.

Vue 앱은 `new Vue` 를 통해 만들어진 root `Vue` 인스턴스로 구성되며 선택적으로 중첩이 가능하고 재사용 가능한 컴포넌트 트리로 구성된다.

예를 들어, ToDo 앱의 컴포넌트 트리는 다음과 같다.

    Root Instance
    |- TodoList
       |- TodoItem
          |- DeleteTodoButton
          |- EditTodoButton
       |- TodoListFooter
          |- ClearTodosButton
          |- TodoListStatistics

확장된 인스턴스를 만들수는 있으나 대개 템플릿에서 사용자 지정 엘리먼트로 선언적으로 작성하는 것이 좋다.

## 속성과 메소드

각 Vue 인스턴스는 `data` 객체에 있는 모든 속성을 **_프록시_** 처리한다.

```js
var data = { a: 1 }

var vm = new Vue({
  data: data
})

vm.a === data.a // => true

vm.a = 2
data.a // => 2
```

데이터가 변경되면 화면은 다시 렌더링된다.

`data`의 속성은 인스턴스가 생성될 때 존재했다면 **_반응형_**이 된다.

하지만,

```js
vm.b = 'hi'
```

이후 `b` 가 변경되어도 화면이 갱신되지 않는다.

반응형 속성이 필요하다면 값을 지정하지 않은 채로 초기화만 하면 된다.

```js
data: {
  newTodoText: '',
  visitCount: 0,
  hidCompletedTodos: false,
  todos: [],
  error: null
}
```

Vue 인스턴스는 데이터 이외에도 많은 인스턴스 속성 및 메소드를 제공하는데, 이는 `$` 접두어로 사용자 정의 속성과 구분한다.

```js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch 는 인스턴스 메소드 이다.
vm.$watch('a', function (newVal, oldVal){
  // vm.a가 변경되면 호출되는 Callback
})
```

## 인스턴스 라이프사이클 훅

각 Vue 인스턴스는 데이터를 관찰(Observe)을 설정하고, 템플릿을 컴파일하고, 인스턴스를 DOM에 마운트하고, 데이터가 변경될 때 DOM을 업데이트해야 할 때 일련의 초기화 단계를 거친다.

그 과정에서 사용자 정의 로직을 실행 할 수 있는 **_라이프사이클 훅_** 도 호출된다.

예를 들어, `created` 훅은 인스턴스가 생성된 후에 호출된다.

```js
new Vue({
  data: {
    a: 1
  },
  created: function(){
    console.log('a is: ' + this.a)
  }
})
```

`mounted`, `updated`, `destroyed` 등 다른 인스턴스 라이프사이클 훅이 존재한다.

모든 라이프사이클 훅은 `this` 컨텍스트가 호출하는 Vue 인스턴스를 가리키며 호출된다.

> 라이프사이클 훅에 화살표함수(=>)를 사용하지 말자.
> 화살표함수는 부모 컨텍스트에 바인딩 되기 때문에 `this`는 Vue 인스턴스가 아니게된다.

## 라이프사이클 다이어그램

아래는 인스턴스 라이프사이클에 대한 다이어그램이다.

![LifeCycle](https://kr.vuejs.org/images/lifecycle.png)