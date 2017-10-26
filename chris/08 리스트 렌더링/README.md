# 리스트 렌더링

## `v-for`로 엘리먼트에 배열 매핑하기

`v-for` 디렉티브를 사용하여 배열을 기반으로 리스트를 렌더링 할 수 있다.

`v-for` 디렉티브는 `item in items` 형태로 특별한 문법이 필요하다.

여기서 `items` 는 원본 데이터 배열이고 `item` 은 반복되는 배열 엘리먼트의 **_별칭_** 입니다.

### 기본 사용방법

```html
<ul id="example-1">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

```js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

`v-for` 블록 안에는 부모 범위 속성에 대한 모든 권한이 있다.

`v-for` 는 또한 현재 항목의 인덱스에 대한 두 번째 전달인자 옵션을 제공한다.

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

```js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Parent',
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

`in` 대신에 `of` 를 구분자로 사용할 수 있다.

JavaScript의 이터레이터에 대한 자바스크립트 구문과 유사하다.

```html
<div v-for="item of items"></div>
```

### `v-for` 와 객체

`v-for` 를 사용하여 객체의 속성을 반복할 수도 있다.

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

```js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
```

키의 두번째 전달 인자를 제공할 수도 있다.

```html
<div v-for="(value, key) in object">
  {{ key }}: {{ value }}
</div>
```

인덱스 역시 제공한다.

```html
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }} : {{ value }}
</div>
```

> 객체를 반복할 때 순서는 `Object.keys()` 의 키 나열 순서에 따라 결정된다. 이 순서는 JavaScript 엔진 구현 간에 **_일관적이지는 않다._**

## `key`

Vue가 `v-for` 에서 렌더링된 엘리먼트 목록을 갱신할 때 기본적으로 "in-place patch" 전략을 사용한다.

데이터 항목의 순서가 변경된 경우 항목의 순서와 일치하도록 DOM 요소를 이동하는 대신 Vue는 각 요소를 적절한 위치에 패치하고 해당 인덱스에서 렌더링할 내용을 반영하는지 확인한다.

Vue가 각 노드의 ID를 추적하고 기존 엘리먼트를 재사용하고 재정렬 할 수 있도록 힌트를 제공하려면 각 항목에 고유한 `key` 속성을 제공해야 한다. `key` 에 대한 이상적인 값은 각 항목의 고유한 ID이다.

```html
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

## 배열 변경 감지

### 변이 메소드

Vue는 감시중인 배열의 변이메소드를 래핑하여 뷰 갱신을 트리거한다. 래핑된 메소드는 다음과 같다.

-   `push()`
-   `pop()`
-   `shift()`
-   `unshift()`
-   `splice()`
-   `sort()`
-   `reverse()`

콘솔에서 이전 예제의 `items` 배열로 변이 메소드를 호출하여 재생할 수 있다.

예: `example1.items.push({ message: 'Baz' })`

### 배열 대체

이름에서 알 수 있듯이 변이 메소드는 호출된 원본 배열을 변형한다.

이와 반대로 변형을 하지 않는 방법도 있다.

`filter()` 와 `concat()` 과 `slice()` 이다.

이 방법을 사용하면 원본 배열을 변형하지 않고 **_항상 새 배열을 반환한다._**

변형이 없는 방법으로 작업할 때 이전 배열을 새 배열로 바꿀 수 있다.

```js
exmaple1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

### 주의 사항

JavaScript의 제한으로 인해 Vue는 배열에 대해 다음과 같은 변경 사항을 감지 할 수 없다.

1.  인덱스로 배열에 있는 항목을 직접 설정하는 경우
    예: `vm.items[indexOfItem] = newValue`
2.  배열 길이를 수정하는 경우,
    예: `vm.items.length = newLength`

주의 사항 중 1번을 극복하기 위해 다음 두 경우 모두 `vm.items[indexOfItem] = newValue` 와 동일하게 수행하며, 반응형 시스템에서도 상태 변경을 트리거한다.

```js
Vue.set(example1.items, indexOfItem, newValue)
```

```js
example1.items.splice(indexOfItem, 1, newValue)
```

주의 사항 중 2번을 극복하기 위해 `splice` 를 사용해야 한다.

```js
example1.items.splice(newLength)
```

## 객체 변경 감지에 관한 주의사항

모던 JavaScript의 한계로 **_Vue는 속성 추가 및 삭제_**를 감지하지 못한다.

예를 들어,

```js
var vm = new Vue({
  data: {
    a: 1
  }
})
// vm.a 는 반응형이다.

vm.b = 2
// vm.b 는 반응형이 아니다.
```

Vue는 이미 만들어진 인스턴스에 새로운 루트레벨의 반응형 속성을 동적으로 추가하는 것을 허용하지 않는다.

하지만 `Vue.set(object, key, value)` 메소드를 사용하여 중첩된 객체에 반응형 속성을 추가할 수 있다.

```js
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
```

다음과 같이 중첩된 `userProfile` 객체에 새로운 속성 `age` 를 추가한다.

```js
Vue.set(vm.userProfile, 'age', 27)
```

인스턴스 메소드인 `vm.$set` 을 사용할 수도 있다. 이는 전역 `Vue.set` 의 별칭이다.

```js
this.$set(this.userProfile, 'age', 27)
```

때로는 `Object.assign()` 이나 `_.extend()` 를 사용해 기존의 객체에 새 속성을 할당 할 수 있다.

이 경우 두 객체의 속성을 사용해 새 객체를 만들어야 한다.

```js
Object.assign(this.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

새로운 반응형 속성을 다음과 같이 추가한다.

```js
this.userProfile = Object.assign({}, this.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

## 필터링 / 정렬 된 결과 표시하기

때로 원본 데이터를 실제로 변경하거나 재설정하지 않고 배열의 필터링 된 버전이나 정렬된 버전을 표시해야 할 필요가 있다.

이 경우 필터링 된 배열이나 정렬된 배열을 반환하는 계산된 속성을 만들 수 있다.

예:

```html
<li v-for="n in evenNumbers">{{ n }}</li>
```

```js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function (){
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

계산된 속성을 실행할 수 없는 상황(예: 중첩 된 `v-for` 루프 내부)에서는 다음 방법을 사용할 수 있다.

```html
<li v-for="n in even(numbers)">{{ n }}</li>
```

```js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

## Range `v-for`

`v-for` 는 정수를 사용할 수 있다. 이 경우 템플릿을 여러번 반복 한다.

```html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

## `v-for` 템플릿

템플릿 `v-if` 와 마찬가지로, `v-for` 와 함께 `<template>` 태그를 사용하여 여러 엘리먼트의 블럭을 렌더링 할 수있다.

예시를 보자.

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider"></li>
  </template>
</ul>
```

## `v-for` 와 `v-if`

동일한 노드에 두가지 모두 있다면, `v-for` 가 `v-if` 보다 높은 우선순위를 갖는다.

즉, `v-if` 는 루프가 반복될 때마다 실행된다.

이는 일부 항목만 렌더링 하려는 경우 유용하다.

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

위의 경우 완료되지 않은 할일만 렌더링 한다.

위 방법 대신 실행을 조건부로 하는 것이 목적이라면 래퍼 엘리먼트(또는 `<template>`)을 사용하자.

```html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```

### 컴포넌트와 `v-for`

> 이 섹션에서는 컴포넌트에 대한 지식이 있다고 가정한다.

일반 엘리먼트처럼 사용자 정의 컴포넌트에서 `v-for` 를 직접 사용할 수 있다.

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

> 2.2.0버전 이후로, `v-for` 를 사용할 때 `key` 가 반드시 있어야한다.

하지만 컴포넌트에서는 그 범위가 분리되어 있기 때문에 컴포넌트에 데이터를 자동으로 전달하지 않는다.

반복된 데이터를 컴포넌트에 전달하려면 `props`를 사용해야 한다.

```html
<my-component
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id">
</my-component>
```

컴포넌트에 `item` 을 자동으로 주입하지 않는 이유는 컴포넌트가 `v-for` 작동 방식과 밀접하게 결합되기 때문이다.

데이터의 출처를 명시적으로 표현하면 다른 상황에서는 컴포넌트를 재사용 할 수 있다.

간단한 할일 목록 전체 예제를 보자.

```html
<div id="todo-list-example">
  <input
    v-model="newTodoText"
    @:keyup.enter="addNewTodo"
    placeholder="Add a todo">
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      :key="todo.id"
      :title="todo.title"
      @remove="todos.splice(index, 1)"></li>
  </ul>
</div>
```

> `is="todo-item` 속성을 보면 `<li>` 엘리먼트는 `<ul>` 안에서만 유효하다. `<todo-item>` 과 같은 일을 하지만 잠재적인 브라우저의 구문 분석 오류를 해결한다. 자세한 내용은 [DOM 템플릿 파싱 주의사항](https://kr.vuejs.org/v2/guide/components.html#X-Templates)을 참조하자.

```js
Vue.component('todo-item', {
  template: '\
  <li>\
    {{ title }}\
    <button @click="$emit(\'remove\')">X</button>\
  </li>\
',
  props: ['title']
})
new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [{
        id: 1,
        title: 'Do the dishes'
      },
      {
        id: 2,
        title: 'Take out the trash'
      },
      {
        id: 3,
        title: 'Mow the lawn'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```
