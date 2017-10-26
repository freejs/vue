# 조건부 렌더링

## `v-if`

Handlebars와 같은 문자열 템플릿에서 다음과 같은 조건부 블록을 작성할 수 있다.

```html
{{ #if ok }}
  <h1>Yes</h1>
{{ /if }}
```

Vue에서는 `v-if` 디렉티브를 사용하여 같은 결과를 얻을 수 있다.

```html
<h1 v-if="ok">Yes</h1>
```

`v-else` 와 함께 "else"블록을 추가하는 것도 가능하다.

```html
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

### `<template>` 에 `v-if` 를 갖는 조건부 그룹 만들기

`v-if` 는 디렉티브이기 때문에 하나의 엘리먼트에 추가해야한다.

이 경우 보이지 않는 래퍼 역할을 하는 `<template>` 엘리먼트에 `v-if` 를 사용할 수 있다.

최종 렌더링 결과에는 `<template>` 엘리먼트가 포함되지 않는다.

```html
<template v-if="ok">
  <h1>Title</h1>
  <p>Paragraph 1</p>
  <p>Paragraph 2</p>
</template>
```

### `v-else`

`v-else` 디렉티브를 사용하여 `v-if` 에 대한 "else" 블록을 나타낼 수 있다.

```html
<div v-if="Math.random() > 0.5">
  이제 나를 볼 수 있어요
</div>
<div v-else>
  이제는 안보입니다
</div>
```

`v-else` 엘리먼트는 `v-if` 엘리먼트 또는 `v-else-if` 엘리먼트 바로 뒤에 있어야 한다. 그렇지 않으면 작동하지 않는다.

### `v-else-if`

> 2.1.0+

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

### `key`를 이용한 재사용 가능한 엘리먼트 제어

Vue는 가능한 한 효율적으로 엘리먼트를 렌더링하려고 시도하며 종종 처음부터 렌더링 하지 않고 재사용 한다.

사용자가 여러 로그인 유형을 전환할 수 있도록 허용하는 예제를 보자.

```html
<template v-if="loginType === 'username'">
  <label>사용자 이름</label>
  <input placeholder="사용자 이름을 입력하세요">
</template>
<template v-else>
  <label>이메일</label>
  <input placeholder="이메일 주소를 입력하세요">
</template>
```

위 코드에서 `loginType` 을 바꾸어도 사용자가 이미 입력한 내용은 지워지지 않는다.

두 템플릿 모두 같은 요소를 사용하므로 `<input>` 은 대체되지 않고 `placholder` 만 변경된다.

하지만 이것은 항상 바람직 하지는 않다.

이 경우 "이 두 엘리먼트는 완전히 별개이므로 다시 사용하지 마십시오" 라고 알리는 방법을 제공한다.

유니크한 값으로 `key` 속성을 추가한다.

```html
<template v-if="loginType === 'username'">
  <label>사용자 이름</label>
  <input placeholder="사용자 이름을 입력하세요" key="username-input">
</template>
<template v-else>
  <label>이메일</label>
  <input placeholder="이메일 주소를 입력하세요" key="email-input">
</template>
```

이제 전환 할 때마다 처음부터 렌더링 된다.

`<label>` 엘리먼트는 `<key>` 속성이 없기 때문에 여전히 효율적으로 재사용 된다.

## `v-show`

엘리먼트를 조건부로 표시하기 위한 또 다른 옵션은 `v-show` 디렉티브이다.

사용법은 거의 동일하다.

```html
<h1 v-show="ok">안녕하세요!</h1>
```

차이점은 `v-show` 가 있는 엘리먼트는 항상 렌더링 되고 DOM에 남아있다는 점이다.

`v-show` 는 단순히 엘리먼트에 `display` CSS 속성을 토글한다.

## `v-if` VS `v-show`

일반적으로 `v-if` 는 토글 비용이 높고, `v-show` 는 초기 렌더링 비용이 더 높다.

자주 바뀐다면 `v-show` 를, 런타임 시 조건이 바뀌지 않으면 `v-if` 를 권장한다.

## `v-if` 와 `v-for`

`v-if` 와 함께 사용 하는 경우, `v-for` 는 `v-if`보다 높은 우선순위를 갖는다.

자세한 내용은 [리스트 렌더링 가이드](https://kr.vuejs.org/v2/guide/list.html#v-for-with-v-if)를 확인하자.