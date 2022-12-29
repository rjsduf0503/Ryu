---
layout: post
author: 류건열
title: '[TIL] TypeScript alias 설정 시 에러 해결하기 + storybook 에러 해결하기'
categories: til
tags: [cnu, javascript, til, today-i-learned, react, typescript, storybook, alias]
---

![header](https://capsule-render.vercel.app/api?type=waving&color=auto&height=300&section=header&text=Today%20I%20Learned...&fontAlign=30&fontAlignY=30&fontSize=55)

# TypeScript에서 alias 설정 시 발생하는 에러를 해결해보자

## 💡 새롭게 알게된 점

프로젝트를 타입스크립트로 초기화하는 도중, 경로에 대한 alias를 추가하는 과정에서 에러에 봉착했다.

단순히 자바스크립트에서 설정한 것 처럼 아래와 같이 설정하면 될 줄 알았는데, 아예 빨간줄이 떠버리는 문제가 발생했다.

```jsx
const path = require('path');

module.exports = {
  babel: {
    presets: ['@emotion/babel-preset-css-prop'],
  },
  webpack: {
    alias: {
      '@components': path.resolve(__dirname, 'src/components'),
      '@hooks': path.resolve(__dirname, 'src/hooks'),
      '@contexts': path.resolve(__dirname, 'src/contexts'),
      '@pages': path.resolve(__dirname, 'src/pages'),
    },
  },
};
```

### craco 라이브러리 설치

그래서 찾아보니 **craco-alias**라는 라이브러리가 추가로 필요하다고 한다.

바로 설치..

craco는 이미 설치를 했었다. 혹시라도 설치가 필요하다면

`npm i @craco/craco`

그 후

`npm i -D craco-alias`

### tsconfig.paths.json

npm을 실행시키는 순간 설정해놓은 tsconfig.json이 초기 설정으로 리셋되는 문제를 막기 위해, path와 연관된 설정들을 따로 모아 세팅할 모듈이 필요하다. 파일의 이름은 아무거나 설정해도 무관하지만, 나중에 craco 관련 모듈에서의 **파일명을 일치**시키는 것이 중요하다.

```json
{
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "@/*": ["src/*"],
      "@components/*": ["src/components/*"],
      "@hooks/*": ["src/hooks/*"],
      "@contexts/*": ["src/contexts/*"],
      "@pages/*": ["src/pages/*"]
    }
  }
}
```

프로젝트의 root 경로에 tsconfig.paths.json 모듈을 생성해주고, baseUrl과 paths를 사용하여 경로 별칭을 설정해준다.

### craco.config.js

craco.config.js에 추가로 설정한 tsconfig 옵션들을 craco-alias 라이브러리를 사용하여 적용하도록 한다.

```jsx
const CracoAlias = require('craco-alias');

module.exports = {
  plugins: [
    {
      plugin: CracoAlias,
      options: {
        source: 'tsconfig',
        baseUrl: './',
        tsConfigPath: 'tsconfig.paths.json',
        debug: false,
      },
    },
  ],
};
```

마찬가지로 프로젝트의 root 경로에 모듈을 생성해주고, baseUrl같은 경우 tsconfig.paths.json과 동일한 baseUrl을 사용해야 한다. 또, tsConfigPath는 path 설정을 추가한 모듈을 선언한다.

### tsconfig.json

```json
{
  // {...}
  "extends": "./tsconfig.paths.json",
  "include": ["src", "craco.config.js"]
}
```

extends 부분에 tsconfig.paths.json, include 부분에 craco.config.js를 추가한다. 이 때 extends는 추가로 설정한 파일을 상속받는 역할, include는 해당 모듈을 포함해주는 역할을 한다.

### package.json

기존 scripts 부분의 start, build 등을 확인해보면 react-scripts ‘명령어’로 등록이 되어 있는 것을 확인할 수 있다. 이 때 우리는 craco를 통해 실행시킬 것 이기 때문에 craco 명령어로 변경해준다.

```json
"scripts": {
	"start": "craco start",
	"build": "craco build"
 },
```

여기까지 하면 ts-cra 상황에서 alias 사용이 가능해진다.

하지만 프로젝트에 storybook을 추가한 경우라면 따로 설정해야할 것들이 남았다…

storybook은 별도의 빌드 환경을 사용하기에 webpack 설정을 따로 해줘야 한다고 한다.

### ./storybook/main.js

```jsx
const path = require('path');

module.exports = {
  // {...}
  webpackFinal: async (config) => {
    config.resolve.alias = {
      ...config.resolve.alias,
      '@': path.resolve(__dirname, '../src/'),
      '@components': path.resolve(__dirname, '../src/components/'),
      '@hooks': path.resolve(__dirname, '../src/hooks/'),
      '@contexts': path.resolve(__dirname, '../src/contexts'),
      '@pages': path.resolve(__dirname, '../src/pages'),
    };
    return config;
  },
};
```

위에서 설정한 경로와 동일하게 작성해주면 이제 storybook에서도 에러가 발생하지 않는 모습을 확인할 수 있다.

---

## ❗️ 느낀점

자바스크립트에서 타입스크립트로 바꾸는 과정에서 craco의 config 파일을 그대로 사용할 수 있다고 생각했던 점을 반성한다..

다음부터 적용하기 전에 무조건 찾아보고 적용하자.

craco 없이 alias 설정이 가능할까? 갑자기 궁금하다. 나중에 찾아봐야겠다.

---

## 📖 참고

[alias 기본 에러 해결 참고](https://junjangsee.tistory.com/entry/React-CRA-Typescript-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C-Module-Alias-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0)

[스토리북 에러 해결 참고](https://plusreturn.com/blog/how-to-resolve-a-path-alias-in-storybook/)