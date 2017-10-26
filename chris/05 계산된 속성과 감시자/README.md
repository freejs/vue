# 계산된 속성과 감시자

## 계산된 속성

템플릿 내에서 사용하는 표현식은 편리하지만 단순한 연산에만 사용해야 한다.

너무 많은 로직을 템플릿에 넣으면 유지보수가 어려워 질 수 있다.

```html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

이러한 복잡한 로직은 반드시 **_계산된 속성_** 을 사용해야 한다.

### 기본 예제

```html
<div id="example">
  <p>원본 메시지: "{{ message }}"</p>
  <p>뒤집히도록 계산된 메시지: "{{ reversedMessage }}"</p>
</div>
```

```js
var vm = new Vue({
  el: '#example',
  data: {
    message: '안녕하세요'
  },
  computed: {
    // 게산된 getter
    reversedMessage: function(){
      return this.message.split('').reverse().join('')
    }
  }
})
```

계산된 속성(`computed`)인 `reversedMessage`를 선언했다.

우리가 제공하는 함수는 `vm.reversedMessage` 속성에 대한 getter 함수로 사용된다.

```js
console.log(vm.reversedMessage) // => '요세하녕안'
vm.message = 'Goodbye'
console.log(vm.reversedMessage) // => 'eybdooG'
```

콘솔을 열고 직접 해보자.

`vm.reversedMessage` 의 값은 항상 `vm.message` 의 값에 의존된다.

일반 속성처럼 템플릿의 계산된 속성에 데이터 바인딩 할 수 있다.

Vue는 `vm.reversedMessage` 가 `vm.message` 에 의존하는 것을 알고 있기 때문에 `vm.message` 가 변경될 때 `vm.reversedMessage` 에 의존하는 바인딩을 모두 업데이트 할 것이다.

### 계산된 캐싱 vs 메소드

표현식에서 메소드를 호출하여 같은 결과를 얻을 수 있다.

```html
<p>뒤집힌 메시지: "{{ reversedMessage() }}"</p>
```

```js
// 컴포넌트 내부
methods: {
  reversedMessage: function (){
    return this.message.split('').reverse().join('')
  }
}
```

계산된 속성 대신 함수를 정의 할 수 있다.

최정 결과는 두가지 방식에 대해 동일하다.

하지만 차이점은 **_계산된 속성은 종속성에 따라 캐시된다는 것이다._**

계산된 속성은 종속성 중 일부가 변경된 경우에만 다시 계산된다.

이것은 `message` 가 변경되지 않는 한, 계산된 속성인 `reversedMessage` 에 대한 다중 접근은 함수를 다시 수행할 필요 없이 이전에 계산된 결과를 즉시 반환한다는 것을 의미한다.

이것은 또한 `Date.now()` 가 반응형 의존성을 가지지 않기 때문에 다음 계산된 속성이 절대로 업데이트 되지 않는 것을 의미한다.

```js
computed: {
  now: function(){
    return Date.now()
  }
}
```

비교해보면, 메소드 호출은 재 렌더링 할 때마다 **_항상_** 메소드를 호출한다.

캐싱이 왜 필요할까?

시간이 많이 소요되는 **_A_** 속성을 가지고 있다면, 거대한 배열을 반복하고 많은 계산을 해야한다. 캐싱하지 않으면 **_A_** 의 getter를 필요한 것보다 더 많이 실행하게 된다.

캐싱을 원하지 않으면 메소드를 사용하자.

### 계산된 속성 vs 감시된 속성

Vue는 Vue 인스턴스의 데이터 변경을 관찰하고 이에 반응하는 보다 일반적인 **_속성 감시_** 방법을 제공한다.

다른 데이터 기반으로 변경 할 필요가 있는 데이터가 있는 경우, 특히 AugularJS를 사용 하던 경우 `watch`를 남용하는 경우가 있다.

하지만 `watch` 콜백보다 계산된 속성을 사용하는 것이 더 좋다.

다음 예제를 보자.

```html
<div id="demo">{{ fullName }}</div>
```

```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val){
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val){
      this.fullName = this.firstName + ' ' + val
    }
  }
})
```

위 코드는 중복이 필수적이다. 계산된 속성을 사용하는 방식과 비교해보자.

```js
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function(){
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

### 계산된 Setter

계산된 속성은 기본적으로 getter만 가지고 있지만, 필요한 경우 setter를 제공할 수 있다.

```js
computed: {
  fullName: {}
    get: function(){
      return this.firstName + ' ' + this.lastName
    },
    set: function (newValue){
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

이제 `vm.fullname = 'John Doe` 를 실행하면 setter가 호출되고 `vm.firstName` 과 `vm.lastName` 이 그에 따라 업데이트 된다.

## 감시자

대부분의 경우 계산된 속성이 더 적합하다. 하지만 사용자 정의 감시자가 필요한 경우가 있다.

그래서 Vue는 `watch` 옵션을 통해 데이터 변경에 반응하는 보다 일반적인 방법을 제공한다.

이는 데이터 변경에 대한 응답으로 비동기식 또는 시간이 많이 소요되는 조작을 수행하려는 경우에 가장 유용하다.

예제를 보자.

```html
<div id="app2">
  <p>
    yes/no 질문을 물어보세요:
    <input v-model="question">
  </p>
  <p>{{ answer }}</p>
</div>
```

```js
<script src="https://unpkg.com/axios@0.12.0/dist/axios.min.js"></script>
<script src="https://unpkg.com/lodash@4.13.1/lodash.min.js"></script>
<script>
  var watchExampleVM = new Vue({
    el: '#app2',
    data: {
      question: '',
      answer: '질문을 하기 전까지는 대답할 수 없습니다.'
    },
    watch: {
      // 질문이 변경 될 때 마다 이 기능이 실행된다.
      question: function (newQuestion) {
        this.answer = '입력을 기다리는 중...'
        this.getAnswer()
      }
    },
    methods: {
      getAnswer: _.debounce(
        function () {
          if (this.question.indexOf('?') === -1) {
            this.answer = '질문에는 일반적으로 물음표가 포함 됩니다. ;-)'
            return
          }
          this.answer = '생각중...'
          axios.get('https://yesno.wtf/api')
            .then(response => this.answer = _.capitalize(response.data.answer))
            .catch(error => this.answer = '에러! API 요청에 오류가 있습니다. ' + error)
        }, 500)
    }
  })
</script>
```

이 경우 `watch` 옵션을 사용하면 비동기 연산(API 엑세스)를 수행하고, 우리가 그 연산을 얼마나 자주 수행하는지 제한하고, 최종 응답을 얻을 때까지 중간 상태를 설정할 수 있다.

계산된 속성은 이러한 기능을 수행할 수 없다.


