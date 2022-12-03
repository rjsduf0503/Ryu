---
layout: post
author: 류건열
title: [TIL] VueJS의 Life Cycle
categories: programmers
tags: [cnu, programmers, dev-course, javascript, til, today-i-learned]
---

![header](https://capsule-render.vercel.app/api?type=waving&color=auto&height=300&section=header&text=Today%20I%20Learned...&fontAlign=30&fontAlignY=30&fontSize=55)

# VueJS의 Life Cycle에 대해서 알아보자

## 💡 새롭게 알게된 점

> VueJS에서 각 Vue 컴포넌트 인스턴스는 생성될 때 데이터 감시 설정, 템플릿 컴파일, 인스턴스를 DOM에 마운트, 데이터의 변경 시 DOM을 업데이트하는 일련의 초기화 단계를 거친다.
>
> 이 과정에서 수명 주기 훅(lifecycle hooks)이라고 불리는 함수를 실행해 특정 단계에서 개발자가 의도하는 로직이 실행될 수 있도록 한다.
>
> **Lifecycle**
>
> > <img width="60%" src="https://user-images.githubusercontent.com/34560965/205456438-95782afd-7260-4474-b931-f2e41f938256.png">
> >
> > Vue 공식 문서에 나와 있는 수명 주기 표이다. 이러한 수명 주기에 대해서 자세히 알아보자
> >
> > 우선 크게 beforeCreated, created, beforeMount, mounted와 beforeUpdate, updated와 beforeUnmount, unmounted 이렇게 3개로 나눌 수 있다.
> >
> > > **beforeCreated, created, beforeMount, mounted**
> > >
> > > > ```html
> > > > <div id="app">
> > > >   <h1>{{msg}}</h1>
> > > > </div>
> > > > ```
> > > >
> > > > ```javascript
> > > > const { createApp } = Vue;
> > > >
> > > > const App = {
> > > >   data() {
> > > >     return {
> > > >       msg: 'Hello Vue!',
> > > >     };
> > > >   },
> > > >   beforeCreate() {
> > > >     console.log('beforeCreate!');
> > > >   },
> > > >   created() {
> > > >     console.log('created!');
> > > >   },
> > > >   beforeMount() {
> > > >     console.log('beforeMount!');
> > > >   },
> > > >   mounted() {
> > > >     console.log('mounted!');
> > > >   },
> > > > };
> > > >
> > > > const vm = createApp(App).mount('#app');
> > > > ```
> > > >
> > > > 위 코드의 결과는 무엇일까?
> > > >
> > > > > <img width="483" alt="vue-life-cycle-1" src="https://user-images.githubusercontent.com/34560965/205456459-9fdf869e-c002-4fef-8408-c66382ab078c.png">
> > > > >
> > > > > 당연하게도 beforeCreated, created, beforeMount, mounted의 순서대로 출력된다. 이 결과는 선언 순서를 바꾸더라도 유지된다.
> > > >
> > > > 그렇다면 각 상태에서 this를 통해 msg라는 data에 접근할 수 있을까? 확인해보자.
> > > >
> > > > ```javascript
> > > > {...}
> > > >   beforeCreate() {
> > > >     console.log('beforeCreate!', this.msg);
> > > >   },
> > > >   created() {
> > > >     console.log('created!', this.msg);
> > > >   },
> > > >   beforeMount() {
> > > >     console.log('beforeMount!', this.msg);
> > > >   },
> > > >   mounted() {
> > > >     console.log('mounted!', this.msg);
> > > >   },
> > > > {...}
> > > > ```
> > > >
> > > > <img width="483" alt="vue-life-cycle-2" src="https://user-images.githubusercontent.com/34560965/205456466-f188b5c9-d63d-4a79-93d2-8ee0d23f8df9.png">
> > > >
> > > > 아래에서 createApp()을 하기 전인 beforeCreate에서는 View Application이 만들어지기 직전에 실행되기 때문에, this를 통해 msg라는 data에 접근할 수 없다.
> > > >
> > > > 그 외의 created, beforeMount, mounted 에서는 접근할 수 있다.
> > > >
> > > > 그렇다면 각 주기에서 DOM에 직접 접근할 수 있을까?
> > > >
> > > > ```javascript
> > > > {...}
> > > >   beforeCreate() {
> > > >     console.log('beforeCreate!', this.msg);
> > > >     console.log(document.querySelector('h1'));
> > > >   },
> > > >   created() {
> > > >     console.log('created!', this.msg);
> > > >     console.log(document.querySelector('h1'));
> > > >   },
> > > >   beforeMount() {
> > > >     console.log('beforeMount!', this.msg);
> > > >     console.log(document.querySelector('h1'));
> > > >   },
> > > >   mounted() {
> > > >     console.log('mounted!', this.msg);
> > > >     console.log(document.querySelector('h1'));
> > > >   },
> > > > {...}
> > > > ```
> > > >
> > > > <img width="483" alt="vue-life-cycle-3" src="https://user-images.githubusercontent.com/34560965/205456474-fb42c3b5-2884-4424-ac61-66dc6927c739.png">
> > > >
> > > > mounted를 제외한 나머지 주기에서는 html 구조와 연결되기 전이기 때문에 접근할 수 없다. 하지만 html 구조와 연결된 직후인 mounted에서는 접근할 수 있다.
> > > >
> > > > **beforeCreate**
> > > >
> > > > > View Application이 만들어지기 직전에 실행된다.
> > > > >
> > > > > 아래에서 createApp()을 하기 전이기 때문에 this를 통해 msg라는 data에 접근할 수 없다.
> > > > >
> > > > > 따라서 활용도가 제일 떨어진다.
> > > >
> > > > **created**
> > > >
> > > > > View Application이 만들어진 직후에 실행된다.
> > > > >
> > > > > this를 통해 data에 접근할 수 있기 때문에 많이 사용되는 라이프 사이클
> > > > >
> > > > > 따라서 활용도가 제일 높다.
> > > >
> > > > **beforeMount**
> > > >
> > > > > View Application이 실제 html 구조와 연결되기 직전에 실행된다.
> > > > >
> > > > > created보다 느리고, mounted 처럼 html 구조를 활용할 수 있는 것도 아니기에 활용도가 떨어진다.
> > > >
> > > > **mounted**
> > > >
> > > > > View Application이 실제 html 구조와 연결된 직후에 실행된다.
> > > > >
> > > > > html 구조(DOM)와 연결된 직후 이기 때문에 여기서만 접근 가능하다.
> > > > >
> > > > > DOM API를 다룰 수 있는 유일한 수명 주기로 created와 더불어 활용도가 제일 높다.
> > >
> > > **beforeUpdate, updated**
> > >
> > > > ```javascript
> > > > {...}
> > > >   beforeUpdate() {
> > > >     console.log('beforeUpdate', this.msg);
> > > >   },
> > > >   updated() {
> > > >     console.log('updated', this.msg);
> > > >   }
> > > > {...}
> > > > ```
> > > >
> > > > 위 코드 실행 시 이름과 유사하게 beforeUpdate는 this.msg가 업데이트 되기 전 값, updated는 업데이트 된 값이 나올까?
> > > >
> > > > 실제로 실행해 보면 그렇지 않다.
> > > >
> > > > <img width="483" alt="vue-life-cycle-4" src="https://user-images.githubusercontent.com/34560965/205456493-bfc2c5f3-a835-4721-b829-a14c1a2d0791.png">
> > > >
> > > > ```javascript
> > > > {...}
> > > >   beforeUpdate() {
> > > >     console.log('beforeUpdate', this.msg);
> > > >     console.log(document.querySelector('h1').textContent);
> > > >   },
> > > >   updated() {
> > > >     console.log('updated', this.msg);
> > > >     console.log(document.querySelector('h1').textContent);
> > > >   }
> > > > {...}
> > > > ```
> > > >
> > > > <img width="483" alt="vue-life-cycle-5" src="https://user-images.githubusercontent.com/34560965/205456500-d0c3ddc5-610b-442e-b52e-9398f96b42c5.png">
> > > >
> > > > 하지만 html의 data가 있는 곳에서는 업데이트 되기 전 값이 출력됨을 알 수 있다.
> > > >
> > > > > 이를 통해 beforeUpdate는 data가 바뀌었을 때, 같이 바뀌는 화면이 업데이트 되기 전의 상태를 의미한다는 것을 알 수 있다.
> > > > >
> > > > > **참고로 updated 훅에서 컴포넌트 상태를 변경하면, 무한 업데이트 루프가 발생할 수 있다고 한다.**
> > >
> > > **beforeUnmount, unmounted**
> > >
> > > > ```javascript
> > > > {...}
> > > >   beforeUpdate() {
> > > >     console.log('beforeUnmount!');
> > > >   },
> > > >   updated() {
> > > >     console.log('unmounted!');
> > > >   }
> > > > {...}
> > > >
> > > > const app = createApp(App);
> > > > app.unmount();
> > > > ```
> > > >
> > > > 앞의 두 예제를 봤으니 이 예시 코드의 결과는 잘 알 수 있다.
> > > >
> > > > <img width="483" alt="vue-life-cycle-6" src="https://user-images.githubusercontent.com/34560965/205456504-259eac21-4f4e-4b30-936f-b4e647878c9f.png">

---

## ❗️ 느낀점

> 여태까지 프레임워크를 공부할 때 라이프 사이클의 중요성을 깨닫지 못했어서 사용 중간에 찾아보는 경우가 많았었는데, 저번 인턴십때 플러터 라이프 사이클을 공부한 다음 사용하니 이해도가 훨씬 높았었던 경험이 있었다.
>
> 이를 생각해 보았을 때 Vue에 대해 자세히 공부하기 전, 라이프 사이클에 대해 먼저 공부하는 것은 충분히 도움이 될 것이라 여겨진다.
>
> Vue는 이번이 처음이라 React와 무엇이 다르고 무엇이 비슷할지, 장단점을 비교하며 재밌게 공부할 수 있을 것 같다.
