# 클래스와 스타일 바인딩

데이터 바인딩은 엘리먼트의 클래스 목록과 인라인 스타일을 조작하기 위해 일반적으로 사용된다.

이 두 속성은 `v-bind` 를 사용하여 처리할 수 있다.

그래서 표현식으로 문자열을 계산하면 되지만, 문자열을 연결하고 만드는 것은 짜증나는 일이고 오류를 발생하기 쉽게 만든다.

이러한 이유로, Vue는 `class` 와 `style` 에 `v-bind` 를 사용할 때 특별히 향상된 기능을 제공한다.

표현식은 문자열 이외에 객체 또는 배열을 이용할 수 있다.

## HTML 클래스 바인딩하기

### 객체 구문

클래스를 동적으로 토글하기 위해 `v-bind:class` 에 객체를 전달할 수 있다.

```html
<div v-bind:class="{ active: isActive }"></div>
```

위 구문은 `active` 클래스의 존재 여부가 데이터 속성 `isActive` 의 값에 의해 결정되는 것을 의미한다.

객체에 필드가 더 있으면 여러 클래스를 토글 할 수 있다.

또한 `v-bind:class` 디렉티브는 일반 `class` 속성과 공존 할 수 있다.

그래서 다음과 같은 템플릿이 가능하다.

```html
<div class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }"></div>
```

데이터:

```js
data: {
  isActive: true,
  hasError: false
}
```

아래와 같이 렌더링 된다.

```html
<div class="static active"></div>
```

`isActive` 또는 `hasError` 가 변경되면 클래스 목록도 그에 따라 업데이트 된다.

예를 들어, `hasError`가 `true`가 되면 클래스 목록은 `"static active text-danger"` 가 된다.

바인딩 된 객체는 인라인 일 필요는 없다.

```html
<div v-bind:class="classObject"></div>
```

```js
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```

결과는 같다.

또한 객체를 반환하는 계산된 속성에도 바인딩 할 수 있다.

이것은 일반적이고 강력한 패턴이다.

```html
<div v-bind:class="classObject"></div>
```

```js
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### 배열 구문

배열을 `v-bind:class` 에 전달하여 클래스 목록을 지정할 수 있다.

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

```js
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

아래와 같이 렌더링 된다.

```html
<div class="active text-danger"></div>
```

목록에 있는 클래스를 조건부 토글 하려면 삼항 연산자를 이용할 수 있다.

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

이것은 항상 `errorClass` 를 적용하지만 `isActive` 가 `true` 일 때 `activeClass` 만 적용된다.

하지만 여러 조건부 클래스가 있는 경우 복잡해 질 수 있다.

그래서 배열 구문 내에서 객체 구문을 사용할 수 있다.

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### 컴포넌트와 함께 사용하는 방법

> 이 섹션은 Vue 컴포넌트에 대해 안다고 가정한다.

사용자 정의 컴포넌트로 `class` 속성을 사용하면, 클래스가 컴포넌트의 루트 엘리먼트에 추가된다.

이 엘리먼트는 기존 클래스를 덮어쓰지는 않는다.

예제를 보자.

```js
Vue.component('my-compnent',{
  template: '<p class="foo bar">Hi</p>'
})
```

사용할 클래스 일부를 추가:

```html
<my-component class="baz boo"></my-component>
```

아래는 렌더링 된 HTML이다.

```html
<p class="foo bar baz boo">Hi</p>
```

클래스 바인딩도 동일하다.

```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

`isActive` 가 참일 때 렌더링 된 HTML 은 다음과 같다.

```html
<p class="foo bar active">Hi</p>
```

## 인라인 스타일 바인딩

### 객체 구문

`v-bind:style` 객체 구문은 매우 직설적이다.

거의 CSS처럼 보이지만 JavaScript 객체이다.

속성이름에 camelCase와 kebab-case (따옴표를 함께 사용해야 한다)를 사용할 수 있다.

```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data: {
  activeColor: 'red',
  fontSize: 30
}
```

스타일 객체에 직접 바인딩하여 템플릿이 더 간결하도록 만드는 것이 좋다.

```html
<div v-bind:style="styleObject"></div>
```

```js
data: {
  styleObject: {
    color: 'red',
    fontSize: '13px'
  }
}
```

객체 구문은 종종 객체를 반환하는 계산된 속성과 함께 사용한다.

### 배열 구문

`v-bind:style` 에 대한 배열 구문은 같은 스타일의 엘리먼트에 여러 개의 스타일 객체를 사용할 수 있게 한다.

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

### 자동 접두사

`v-bind:style` 에 브라우저 벤더 접두어가 필요한 CSS 속성 (예: `transform`)을 사용하면 Vue는 자동으로 해당 접두어를 감지하여 스타일을 적용한다.

### 다중 값 제공

> 2.3.0+

2.3버전부터 스타일 속성에 접두사가 있는 여러 값을 배열로 전달 할 수 있다.

예제를 보자.

```html
<div v-bind:style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

브라우저가 지원하는 배열의 마지막 값만 렌더링 한다.

이 예제에서는 flexbox의 접두어가 붙지 않은 버전을 지원하는 브라우저에 대해 `display : flex` 를 렌더링 한다.
