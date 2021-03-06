## vue.js

뷰는 프론트엔드 프레임워크.   
화면을 구성하기 위해 필요한 구성요소부터 팁까지.  

- PWA 기반 Single Page App 을 제작하기 위한 화면 앞단 라이브러리 Vue.js 소개  
(PWQ는 최신 웹개발 패러다임 참고 https://joshua1988.github.io/web-development/opinions/where-are-we-and-where-are-we-heading/ )
- Vue로 화면을 구성하기 위한 기본적인 개념을 학습하고 샘플을 활용하여 코드 제작
- Vue의 주요 구성요소 (Component, Router, Resource, Templates) 를 학습 및 실습

### Vue는 무엇인가?
MVVM 패턴의 ViewModel 레이어에 해당하는 View 단 라이브러리

View에 변화가 있을 때 View 모델이 감지해서 Model 에 신호를 보내고
Model에서 변화된 데이터에 대해서 다시 View 모델을 거쳐서 모델에 보냈을 때
화면이 Reative 하게 반응이 어나는데, 이러한 것이 가능하게끔 모든 것들을 제어하는 것이
View.js 즉 View Model 라이브러리라고 보면 된다.  

**화면 단위를 컴퍼넨트 단위로 제공한다.**  
자체적으로 내장 API 를 지원한다.  
화면에서 변화가 일어났을 때 뒤쪽 뷰 모델(데이터)에서 바로 반응해서 다시 한번 화면으로 보게끔.  
항상 뒤쪽 데이터 모델과 앞쪽 뷰의 표현하는 단위가 동일하다.  
Virtual DOM 을 이용한 렌더링 방식이 React 와 거의 유사.  
다른 프론트엔드 FW와 비교했을 때 훨씬 가볍고 빠르다.  
간단한 뷰를 적용하는데 있어서도 러닝커브가 낮고, 쉽게 접근 가능.  

#### Vue 시작하기

다른 주요 프로트엔드 프레임워크(Angular, React)보다 뷰가 가지는 가장 큰 강점은 바로 시작하기가 정말 쉽다는 점이다.  

#### MVVM 패턴이란?
MVC 패턴에서 파생된 것이다. 화면 앞단에 (화면 관련된)화면을 제어하는 로직들은 별도로 분리를 하고
화면에서 어떠한 리스트를 띄울 때, 그 리스트 데이터 출처가 데이터베이스라던지 특정한 저장소에서 불러오는 것이라면 그 로직은 또 별도로 분리함으로써 프로젝트를 수행함으로써 좀 더 가독성이 올라가고 백앤드나 프론트엔드 개발자들의 협업을 위한 새로운 구조이다.

#### 인스턴스

`helloworld.html` 참고


```javascript
var vm = new Vue({
  //...
  })
```

`vm` 이 하나의 인스턴스이다. 뷰 라이브러리를 로딩했을 때 `Vue` 라는 생성자에 접근이 가능하고, 이 안에 화면에서 사용할 데이터, 속성, 메서드 등등의 옵션을 포함하여 화면의 단위를 생성한다.
말그대로 뷰 모델 레이어의 제어 권한을 갖게 된다. 결론적으로 인스턴스는 뷰로 화면을 개발함에 있어서 가장 중요한 요소이다. 컴포넌트와 밀접한 관계가 있다. 암튼 기반 개념.  

```javascript
//vm은 ViewModel을 뜻한다. (관행적인 코딩 컨벤션)
var vm  = new Vue({
  //options
  })
```

위처럼 생성자를 이용해서 생성 가능. 뷰 안에 화면에 관련된 옵션들을 넣어주고 vm 안에 담아주면 그것이 바로 인스턴스가 생성되는 것이고 화면을 띄울 수 있게 된다.  

```javascript
var vm = new Vue({
  // 화면에 나타나는 한 개의 요소들. div, p, button 등의 정적인 태그 + 모델의 데이터들을 앞단으로 연결해 줄 수 있는 값들
  template: ...,
  // 뷰가 화면이 그려지는 시작점이라고 보면 되는데 el에 지정한 아이디나 클래스로 접근한 특정 태그부터 화면이 그려진다.
  el: ...,
  // 클릭하거나 기타 http요청에 대한 구현 메소드를 안에 선언할 수 있다.
  methods: {
  },
    // 라이프 사이클 훅
  created: {
  }
  // ...
})
```

옵션은 이런식으로 ...  

```javascript
var MyComponent = Vue.extend({
  //template, el, method 와 같은 options 정의
  template: `<p>Hello {{message}}</p>`,
  data: {
    message: 'VUe'
  }
  //...
})
//위에서 정의한 내용 (template, data, ...)을 기본으로 하는 컴포넌트 생성
var MyComponentInstance = new MyComponent()
```

인스턴스는 Vue.extend 라는 확장 기법을 사용하여 재사용이 가능하다. 이렇게 사용하는 경우는 기타 옵션 값들을 추가로 포함하거나 미리 정의해놓고 편하게 쓸 수 있게 하려고 사용한다. 따라서 `Vue.extend`에서 괄호와 같은 속성들을 정의를 하고 `MyCOmponent` 에서 new로 생성하고 다른 인스턴스에 옮기는, 다른 객체에 값을 저장하여 재사용하는 케이스들이 있다. 하지만 공식문서에서는 컴포넌트를 직접 import 하는 것을 권고하고 있다.  


#### Vue Instance 라이프싸이클

라이프 싸이클이라는 것은 어플리케이션이 생성되거나 변경되거나 소멸될 때 각각의 단계들을 의미를 한다. 그래서 intance 라이프 싸이클이라는 것은 객체가 생성되거나 변경되거나 소멸될 때 각각의 어떠한 작업들을 할 것인지 그것을 분기시켜주는 기능이다.  
객체가 생성될 때 아래의 초기화 작업을 수행한다.
- 데이터 관찰 (뷰 딴에서는 어떠한 데이터가 변화를 했을 때 감지할 수 있도록 기능을 갖고 있는 것)
- 템플릿 컴파일 (앞 딴의 화면에서 기본적인 html 이 아니라 뷰가 갖고 있는 데이터들을 html 에 넘겨줘서 html이 뷰의 내용까지 반영한 웹 문서화를 될 수 있게끔 변형해주는 것)
- DOM 에 객체 연결 (뷰 js 의 특성들, 성격들, 옵션들을 갖고 있는 객체들을 DOM에 연결을 해서 이런 부분들이 뷰에서 리액티브하다라는 표현이 될 수 있는 근간이다. 말 그대로 돔이 변경되거나 삭제될 때 뷰에서 자연스럽게 변화를 나타낼 수 있는, 즉 화면을 재갱신할 수 있는 부분)
- 데이터 변경시 DOM 업데이트 (DOM에 객체 연결과 설명 거의 동일)

```javascript
var vm = new Vue({
  data: {
    a: 1
  },
  created: function(){
    //this는 vm을 가리킴
    console.log('a is: '+ this.a)
  }
})
```

초기화 작업 외에도 개발자가 의도하는 커스텀 로직을 라이프싸이클 단계별로 위와 같이 추가할 수 있다.

`created` 속성은 인스턴스가 생성됬을 때 원하는 로직들을 추가해서 구현할 수 있는 부분이다.  
`vm` 은 뷰모델의 관행상 약어  

이 외에도 라이프싸이클 단계에 따라 아래 메서드를 사용할 수 있다.

- `mounted`
- `updated`
- `destroyed`

위와 같이 초기화 메서드로 커스텀 로직을 수행하기 때문에 Vue에서는 따로 Controller를 갖고 있지 않다.  

#### LifeCycle Diagram

순서도 찾아서 구현시 참고  

`lifecycle.html` 참고


#### 컴포넌트

**컴포넌트란**  
화면에 비춰지는 뷰의 단위를 쪼개어 재활용이 가능한 형태로 관리하는 것  
컴포넌트라는 것은 화면의 한 단위. 이 단위가 세부적으로 쪼개지게 되면 상위 컴포넌트, 하위 컴포넌트가 된다. 상위, 하위의 컴포넌트 방식이 뷰에서 지정해놓은 규격이라고 할 수 있다.  

- global component

```javascript
Vue.component('my-component', {
  // ...
})
```


- local component

```javascript
var cmp = {
  data: function(){
    return{
      // ...
    };
  }
  template: '<hr>',
  methods: {}
}

// 아래 Vue 인스턴스에서만 활용할 수 있는 로컬(지역) 컴포넌트 등록
new Vue({
  components: {
    'my-cmp' : cmp
  }
})
```


```javascript

<html>
  <head>
    <title>Vue Sample</title>
  </head>
  <body>
    <div id="app">
      <button>Parent Component</button>
      <my-component></my-component>
      <my-local-component></my-local-component>
    </div>

    <div id="app2">
      <my-component></my-component>
      <my-local-component></my-local-component>
    </div>

    <script src="https://unpkg.com/vue@2.3.3"></script>
    <script>
      // Global Component
      Vue.component('my-component', {
        template: '<div>A global component!</div>'
      });
      // Local Component
      var cmp = {
        template: '<div>A local component!</div>'
      };
      new Vue({
        el: '#app',
        components: {
          // 태그명 : 컴포넌트의 내용
          'my-local-component': cmp
        }
      })
      new Vue({
        el: '#app2',
        components: {
          // 태그명 : 컴포넌트의 내용
          'my-local-component': cmp
        }
      })
    </script>
  </body>
</html>
```
