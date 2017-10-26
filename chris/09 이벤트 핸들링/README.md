# 이벤트 핸들링

## 이벤트 청취

`v-on` 디렉티브를 사용하여 DOM 이벤트를 듣고 트리거 될 때 JavaScript를 실행 할 수 있다.

예제를 보자.

```html
<div id="example-1">
  <button @click="counter += 1">Add 1</button>
  <p>위 버튼을 클릭한 횟수는 {{ counter }} 번 입니다.</p>
</div>
```

```js
var example1 = new Vue({
  el: '#example-1',
  data: {
    counter: 0
  }
})
```

## 메소드 이벤트 핸들러

많은 이벤트 핸들러의 로직은 더 복잡할 것이므로, JavaScript를 `v-on` 속성 값으로 보관하는 것은 간단하지 않다.

이 때문에 `v-on` 이 호출하고자 하는 메소드의 이름을 받는 이유이다.

예제를 보자.

```html
<div id="example-2">
  <button @click="greet">Greet</button>
</div>
```

```js
var example2 = new Vue({
  el: '#example-2',
  data: {
    name: 'Vue.js'
  },
  methods: {
    greet: function (event){
      alert('Hello ' + this.name + '!')
      if(event){
        alert(event.target.tagName)
      }
    }
  }
})

// 또한 JavaScript를 이용해서 메소드를 호출할 수 있다.
example2.greet()
```

## 인라인 메소드 핸들러

메소드 이름을 직접 바인딩 하는 대신 인라인 JavaScript 구문에 메소드를 사용할 수도 있다.

```html
<div id="example-3">
  <button @click="say('hi')">Say hi</button>
  <button @click="say('what')">Say what</button>
</div>
```

```js
new Vue({
  el: '#example-3',
  methods: {
    say: function (message){
      alert(message)
    }
  }
})
```

때로 인라인 명령문 핸들러에서 원본 DOM 이벤트에 액세스 해야 할 수도 있다.

특별한 `$event` 변수를 사용해 메소드에 전달할 수도 있다.

```html
<button @click="warn('Form cannot be submitted yet.', $event)">Submit</button>
```

```js
methods: {
  warn: function (message, event){
    if(event) event.preventDefault()
    alert(message)
  }
}
```

## 이벤트 수식어

이벤트 핸들러 내부에서 `event.preventDefault()` 또는  `event.stopPropagation()` 를 호출하는 것은 매우 보편적인 일이다.

메소드 내에서 쉽게 이 작업을 할 수 있지만, DOM 이벤트 세부 사항을 처리하는 대신 데이터 로직에 대한 메소드만 사용할 수 있으면 더 좋을 것이다.

이 문제를 해결하기 위해, Vue는 `v-on` 이벤트에 **_이벤트 수식어_**를 제공한다.

수식어는 점으로 표시된 접미사 이다.

-   `.stop`
-   `.prevent`
-   `.capture`
-   `.self`
-   `.once`

```html
<!-- 클릭 이벤트 전파가 중단된다 -->
<a @click.stop="doThis"></a>

<!-- 제출 이벤트가 페이지를 다시 로드하지 않는다 -->
<form @submit.prevent="onSubmit"></form>

<!-- 수식어는 체이닝이 가능하다 -->
<a @click.stop.prevent="doThat"></a>

<!-- 단순히 수식어만 사용할 수 있다 -->
<form @submit.prevent></from>

<!-- 이벤트 리스너를 추가할 때 캡쳐모드를 사용한다 -->
<!-- 즉, 내부 엘리먼트를 대상으로 하는 이벤트가 해당 엘리먼트에서 처리되기 전에 여기서 처리한다 -->
<div @click.capture="doThis">...</div>

<!-- event.target이 엘리먼트 자체인 경우에만 트리거를 처리한다 -->
<!-- 자식 엘리먼트에서는 불가능하다 -->
<div @click.self="doThat">...</div>
```

> 관련 코드가 동일한 순서로 생성되므로 수식어를 사용할 때 순서를 지정하자. 다시말해 `@click.prevent.self` 를 사용하면 **_모든 클릭_**을 막을 수 있으며 `@click.self.prevent` 는 엘리먼트 자체에 대한 클릭만 방지한다.
>
> 2.1.4에 추가됨

```html
<!-- 클릭 이벤트는 최대 한번만 트리거 된다 -->
<a @click.once="doThis"></a>
```

네이티브 DOM 이벤트에 독점적인 다른 수식어와 달리, `.once` 수식어는 컴포넌트 이벤트에서도 사용할 수 있다.

## 키 수식어

키보드 이벤트를 청취할 때, 종종 공통 키 코드를 확인해야 한다.

Vue는 키 이벤트를 수신할 때 `v-on` 에 대한 키 수식어를 추가할 수 있다.

```html
<!-- keyCode가 13일 때만 vm.submit()을 호출한다 -->
<input @keyup.13="submit">
```

모든 keyCode를 기억하는 것은 번거롭기 때문에 Vue는 가장 일반적으로 사용되는 키의 별칭을 제공한다.

```html
<input @keyup.enter="submit">
```

아래는 키 수식어 별칭의 전체 목록이다.

-   `.enter`
-   `.tab`
-   `.delete` ("Delete"와 "BackSpace"키 모두를 캡처한다.)
-   `.esc`
-   `.space`
-   `.up`
-   `.down`
-   `.left`
-   `.right`

또한 전역 `config.keyCodes` 객체를 통해 사용자 지정 키 수식어 별칭을 지정할 수 있다.

```js
// @keyup.f1 이 가능해진다
Vue.config.keyCodes.f1 = 112
```

## 수식어 키 목록

> 2.1.0+

다음 수식어를 사용해 해당 수식어 키가 눌려진 경우에만 마우스 또는 키보드 이벤트 리스너를 트리거 할 수 있다.

-   `.ctrl`
-   `.alt`
-   `.shift`
-   `.meta`

예제:

```html
<!-- Alt + C -->
<input @keyup.alt.67="clear">

<!-- Ctrl + Click -->
<input @click.ctrl="doSomething">Do something</div>
```

> 수식어 키는 일반 키와 다르며 `keyup` 이벤트와 함께 사용되면 이벤트가 발생할 때 수식어 키가 눌려있어야 한다. 즉, `keyup.ctrl` 은 `ctrl` 을 누른 상태에서 키를 놓으면 트리거 된다. `ctrl` 키만 놓으면 트리거 되지 않는다.

### 마우스 버튼 수식어

> 2.2.0+

-   `.left`
-   `.right`
-   `.middle`

## 왜 HTML로 된 리스너를 사용할까?

이 모든 이벤트 청취 접근 방법이 우려 사항 분리("seperation of concerns")에 대한 오래된 규칙을 어긴다고 생각할 수 있다.

모든 뷰 핸들러 함수와 표현식은 현재 뷰 처리하는 ViewModel에 엄격히 바인딩 되기 때문에 유지보수가 어렵지 않다.

실제로 `v-on` 을 사용하면 몇가지 이점이 있다.

1.  HTML 템플릿을 간단히 하여 JavaScript 코드 내에서 핸들러 함수 구현을 찾는 것이 더 쉽다.

2.  JavaScript에서 이벤트 리스너를 수동으로 연결할 필요가 없으므로 ViewModel 코드는 순수 로직과 DOM이 필요하지 않다. 테스트가 쉬워진다.

3.  ViewModel이 파기되면 모든 이벤트 리스너가 자동으로 제거된다.
