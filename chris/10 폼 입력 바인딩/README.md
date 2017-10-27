# 폼 입력 바인딩

## 기본 사용법

`v-model` 디렉티브를 사용하여 폼 input과 textarea 엘리먼트에 양방향 데이터 바인딩을 생성 할 수 있다.

입력 유형에 따라 엘리먼트를 업데이트 하는 올바른 방법을 자동으로 선택한다.

약간 이상하지만 `v-model` 은 기본적으로 사용자 입력 이벤트에 대한 데이터를 업데이트하는 "syntax sugar"이며 일부 경우에 특별한 주의를 해야한다.

> `v-model` 은 모든 form 엘리먼트의 초기 `value` 와 `checked` 그리고 `selected` 속성을 무시한다. 항상 Vue 인스턴스 데이터를 원본 소스로 취급한다. 컴포넌트의 `data` 옵션 안에 있는 JavaScript에서 초기값을 선언해야한다.
>
> 중국어, 일본어, 한국어가 필요한 언어의 경우 IME 중 `v-model` 이 업데이트되지 않는다. 이러한 업데이트를 처리하려면 `input` 이벤트를 대신 사용해야한다.

### 문자열

```html
<input v-model="message" placeholder="여기를 수정해보세요">
<p>메시지: {{ message }}</p>
```

> 영문 텍스트는 즉시 반영되지만 한글 등은 한글자가 완성되어야 반영되는 것을 볼 수 있다.

### 여러줄을 가진 문장

```html
<span>여러 줄을 가지는 메시지:</span>
<p style="white-space: pre-line">{{ message }}</p>
<br>
<textarea v-model="message" placeholder="여러줄을 입력해보세요"></textarea>
```

> 텍스트 영역의 보간 (`<textarea>{{ text }}</textarea>`)은 작동하지 않는다. 대신 `v-model` 을 사용하자.

### 체크박스

하나의 체크박스는 단일 boolean 값을 가진다.

```html
<input type="checkbox" id="checkbox" v-mode="checked">
<label for="checkbox">{{ checked }}</label>
```

여러개의 체크박스는 같은 배열을 바인딩 할 수 있다.

```html
<div id="example-3">
  <input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
  <input type="checkbox" id="john" value="John" v-model="checkedNames">
  <input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
  <br>
  <span>체크한 이름: {{ checkedNames }}</span>
</div>
```

```js
new Vue({
  el: '#example-3',
  data: {
    checkedNames: []
  }
})
```

### 라디오

```html
<input type="radio" id="one" value="One" v-model="picked">
<label for="one">One</label>
<br>
<input type="radio" id="two" value="Two" v-model="picked">
<label for="two">Two</label>
<br>
<span>선택: {{ picked }}</span>
```

### 셀렉트

하나의 셀렉트

```html
<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<span>선택함: {{ selected }}</span>
```

```js
new Vue({
  el: '...',
  data: {
    selected: ''
  }
})
```

> `v-model` 표현식의 초기 값이 어떤 옵션에도 없으면, `<select>` 엘리먼트는 "선택없음" 상태로 렌더링된다. iOS에서는 이 경우 변경 이벤트가 발생하지 않아 사용자가 첫 번째 항목을 선택할 수 없게 된다. 따라서 위 예제처럼 사용하지 않는 옵션에 빈 값을 넣는 것이 좋다.

다중 셀렉트 (배열바인딩):

```html
<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
<br>
<span>Selected: {{ selected }}</span>
```

`v-for` 를 이용한 동적 옵션 렌더링

```html
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>
```

```js
new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})
```

## 값 바인딩하기

라디오, 체크박스 및 셀렉트 옵션의 경우, `v-model` 바인딩 값은 보통 정적인 문자열(또는 체크 박스의 boolean) 이다.

```html
<!-- picked 는 선택시 문자열 "a" 이다 -->
<input type="radio" v-model="picked" value="a">

<!-- toggle 은 true 또는 false 이다 -->
<input type="checkbox" v-model="toggle">

<!-- selected 는 "ABC" 선택시 "abc" 이다 -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

그러나 때로 값을 Vue 인스턴스에 동적 속성에 바인딩 해야할 수 있다. `v-bind` 를 사용할 수 있다.

`v-bind` 를 사용하면 입력 값을 문자열이 아닌 값에 바인딩 할 수도 있다.

### 체크박스

```html
<input
  type="checkbox"
  v-model="toggle"
  :true-value="a"
  :false-value="b">
```

### 라디오

```html
<input type="radio" v-model="pick" :value="a">
```

```js
vm.pick === vm.a
```

### 셀렉트 옵션

```html
<select v-model="selected">
  <option :value="{ number: 123 }">123</option>
</select>
```

```js
// 선택 이후
typeof vm.selected // => 'object'
vm.selected.number // => 123
```

## 수식어

### `.lazy`

기본적으로, `v-model` 은 각 입력 이벤트 후 입력과 데이터를 동기화 한다.

`.lazy` 수식어를 추가하여 `change` 이벤트 이후에 동기화 할 수 있다.

```html
<input v-model.lazy="msg">
```

### `.number`

사용자 입력이 자동으로 숫자로 형변환 되기를 원하면, `v-model` 이 관리하는 input에 `number` 수식어를 추가하면 된다.

```html
<input v-model.number="age" type="number">
```

`type="number"` 를 사용하는 경우에도 HTML 입력 엘리먼트의 값은 항상 문자열을 반환하기 때문에 이것은 종종 유용하게 사용할 수 있다.

### `.trim`

`v-model` 이 관리하는 input을 자동으로 trim하기 원하면, `trim` 수정자를 추가하면 된다.

```html
<input v-model.trim="msg">
```
