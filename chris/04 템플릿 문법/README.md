# 템플릿 문법

Vue.js는 렌더링 된 DOM을 기본 Vue 인스턴스 데이터에 선언적으로 바인딩 할 수 있는 HTML 기반 템플릿 구문을 사용한다.

내부적으로 Vue는 템플릿을 가상 DOM 렌더링 함수로 컴파일 한다.

## 보간법(Interpolation)

### 문자열

데이터 바인딩의 가장 기본 형태는 "Mustache"(콧수염) 구문(이중 중괄호)을 사용한 텍스트 보간이다.

```html
<span>메시지: {{ msg }}</span>
```

Mustache 태그는 해당 데이터 객체의 `msg` 속성 값으로 대체된다.

또한 데이터 객체의 `msg` 속성이 변경될 때 마다 갱신된다.

`v-once` 디렉티브를 사용하여 데이터 변경 시 업데이트 되지 않는 일회성 보간을 수행할 수 있지만, 같은 노드의 바인딩에도 영향을 미친다는 점을 유의해야 한다.

```html
<span v-once>다시는 변경하지 않는다: {{ msg }}</span>
```

### 원시 HTML

이중 중괄호(Mustache)는 HTML이 아닌 일반 텍스트로 데이터를 해석한다.

실제 HTML을 출력하려면 `v-html` 디렉티브를 사용해야 한다.

```html
<div v-html="rawHtml"/>
```

이 div의 내용은 일반 HTML로 해석되는 `rawHtml` 속성의 값으로 대체된다.

데이터 바인딩은 무시된다.

### 속성

Mustaches는 HTML 속성으로 사용할 수 없으며 대신 `v-bind` 디렉티브를 사용해야 한다.

```html
<div v-bind:id="dynamicId"/>
```

Boolean 속성에 대해서도 작동한다. 조건이 거짓이면 속성이 제거된다.

```html
<button v-bind:disabled="isButtonDisabled">Button</Button>
```

### JavaScript 표현식 사용하기

지금까지는 템플릿의 간단한 속성 키에만 바인딩 했다.

하지만 Vue.js는 모든 데이터 바인딩에 JavaScript 표현식의 모든 기능을 지원한다.

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

위 표현식은 Vue 인스턴스 데이터 범위 내에서 JavaScript로 계산된다.

한가지 제한사항은 각 바인딩마다 **_하나의 단일 표현식_** 만 포함 될 수 있으므로 아래처럼 작성 할 수 없다.

```html
{{ var a = 1 }}

{{ if (ok) { return message } }}
```

> 템플릿 표현식은 샌드박스 처리 되어서 `Math` 나 `Date` 같은 전역으로 사용 가능한 것에만 적븐 할 수 있다. 따라서 템플릿 표현식에서 사용자 정의 전역에 엑세스 할 수 없다.

## 디렉티브

디렉티브는  `v-` 접두사가 있는 특수 속성이다.

디렉티브의 속성 값은 **_단일 JavaScript 표현식_**이 된다.(`v-for`는 예외)

디렉티브의 역할은 표현식의 값이 변경될 때 사이드이펙트를 반응적으로 DOM에 적용하는 것이다.

예제를 보자.

```html
<p v-if="seen">이제 나를 볼 수 있어요.</p>
```

여기서, `v-if` 디렉티브는 `seen` 값에 따라 `<p>` 엘리먼트를 제거 또는 삽입한다.

### 전달인자

일부 디렉티브는 콜론으로 표시되는 "전달인자"를 사용할 수 있다.

예를 들어, `v-bind` 디렉티브는 반응적으로 HTML 속성을 갱신하는데 사용된다.

```html
<a v-bind:href="url"></a>
```

여기서 `href` 는 전달인자로, 엘리먼트의 `href` 속성을 표현식 `url` 의 값에 바인드하는 `v-bind` 디렉티브에게 알려준다.

또 다른 예로 DOM 이벤트를 수신하는 `v-on` 디렉티브이다.

```html
<a v-on:click="doSomething"></a>
```

전달인자는 이벤트를 받을 이름이다.

### 수식어

수식어는 점(.)으로 표시되는 특수 접미사로, 디렉티브를 특별한 방법으로 바인딩 해야 함을 나타낸다.

예를 들어, `.prevent` 수식어는 트리거된 이벤트에서 `event.preventDefault()` 를 호출하도록 `v-on` 디렉티브에게 알려준다.

```html
<form v-on:submit.prevent="onSubmit"></form>
```

## 약어

자주 사용되는 `v-bind` 와 `v-on` 디렉티브에 대해 특별한 약어를 제공한다.

### `v-bind` 약어

```html
<!-- 전체 구문 -->
<a v-bind:href="url"></a>

<!-- 약어 -->
<a :href="url"></a>
```

### `v-on` 약어

```html
<!-- 전체 구문 -->
<a v-on:click="doSomething"></a>

<!-- 약어 -->
<a @click="doSomething"></a>
```

이는 최종 렌더링 된 마크업에는 나타나지 않는다.