# 서버사이드 렌더링 그리고 Next.js

서버에서 리액트 코드를 실행해서 렌더링 하는 서버사이드 렌더링.

1. SEO를 해야 한다.
2. 빠른 첫 페이지 렌더링이 중요하다.

라는 조건일 때 고려한다.

사실 구글을 제외한 다른 검색엔진에선 자바스크립트를 지원하지 않아, 빈 div만 있는 사이트와 동일하다.  
따라서 SEO를 신경쓴다면 SSR을 생각해야 한다. 구글 검색엔진도 SSR에 더 높은 점수를 부여한다고 알려져있다.  
또한 서버에서 미리 첫 페이지를 렌더링 하기에 인터넷 속도가 느린 네트워크 인프라가 약한 나라에서 서비스 해야한다면 SSR을 고려하는게 좋다.  
Next라는 프레임워크 없이 처음엔 react로 직접 SSR을 구축해보자.

<br>

## SSR 초급편

next없이 웹팩과 바벨로 SSR을 하고 싶다면,

1.  `express`로 노드 서버를 띄워 `renderToString`함수의 도움을 받아 `html`을 미리 완성시킨다.
2.  `ReactDom.render` 가 아닌 `ReactDom.hydrate`를 통해 이벤트 처리 함수를 붙여줘 SSR에서 CSR로 전환시킨다.

두가지 과정만 거치면 된다.

만약 js파일만 번들링 한것이라면 client쪽에서만 웹팩을 활용해 번들링 해도 상관 없는데, 다른 파일들을 모듈로 적용시키려면 server까지 웹팩을 활용해 파일의 경로를 맞춰줘야 한다.  
파일의 경로가 달라지면 파일을 client에서 정상적으로 활용할 수 없다.

<br>

### 클라이언트에서만 렌더링

웹팩을 설정해준다.

1. js파일은 바벨 로더로 처리한다.
2. `HtmlWebpackPlugin`을 통해 `index.html`의 템플릿을 정해준다.

바벨을 설정해준다.

- 프리셋으로 `@babel/preset-react`, `@babel/preset-env`를 설정해준다.

<br>

### express로 서버 만들기

서버에서도 JSX문법을 실행해야해서,ESM을 써야해서 바벨이 필요하다.  
서버와 클라이언트에서 쓰는 바벨 설정이 다르므로 .bablerc.common과 client,server로 분리해준다.

`server.js`를 만들고 서버 관련 코드를 작성한다.  
웹팩을 통해 클라이언트에서 빌드된 index.html를 가져오고, 최상위 컴포넌트 App을 가져와 renderToString함수로 변환하고, index.html에 넣어준다.
이제 `index.js`에서 ReactDom.render 를 hydrate로 바꿔준다.

<br>

### 서버의 데이터 전달

클라이언트에서 빌드된 html을 가져와서 미리 랜더링을 시켜주는게 SSR이다.  
html을 가져와 여러가지를 할 수 있는데, 빌드되기 전 html 템플릿에 상수를 미리 작성해두고, 서버에서 renderToString 시킬때 같이 replace를 시켜주는 방식으로 서버->클라이언트 데이터 전송이 일어난다.

<br>

클라이언트에서 받아쓸땐 window객체의 프로퍼티를 활용한다.

<br>

### 스타일 적용

css-in-js 방식의 경우, 서버에서 작업을 해두지 않는다면 깜빡거림이 발생한다.  
따라서 `server.js`에서 추가적인 작업이 필요하다.

<br>

### 이미지 모듈 적용

서버 코드도 웹팩으로 번들링 하기 위해선 webpack.config를 변경해야한다.  
webpack.config 안에 함수를 만들어 조건부로 달라지는 객체를 반환시킨 후, `module.exports`에 배열로 두개의 원소를 넣어주면 두번 실행된다. 웹팩설정파일에서 배열을 내보낼 경우 length만큼 실행된다.

<br>

## 서버사이드 렌더링 고급편

SSR은 서버 리소스를 많이 사용하는데, 특히 렌더링할때 CPU를 많이 쓴다. 따라서 높은 트레픽이 몰릴때 빠르게 응답하기 위해서 일정 부분의 부하 이상엔 CSR을 시키는것도 한 방법이다. 단 SEO가 제일 중요하다고 할땐 우선순위를 따져봐야한다.  
아예 정적인 페이지.

1. 바뀔 일이 거의 없는 페이지라면 완전히 서버에서 렌더링을 해놓고 정적 페이지를 서비스 하는게 낫다.
2. 페이지에서 바뀌는 부분이 작다고 할땐, 특정 부분만 SSR을 적용할 수 도 있다.

<br>

### 페이지 미리 렌더링하기

1. 화면의 일부를 클라이언트에서 렌더링  
   페이지 컴포넌트의 props로 바뀌어야 하는 데이터를 넘기는데, 이 데이터는 client에서 심어준다.

2. 일부 페이지를 서버에서 미리 렌더링
   유틸 함수를 만들어, server.js에서 조건부로 랜더링 시킬지, 렌더링된것을 보여줄지 코드를 작성한다.  
   웹팩에서도 빌드를 해줘야 하므로 웹팩.config도 수정한다.

<br>

### 서버사이드 렌더링 캐싱하기

`lru-cache` 라이브러리를 이용한다.  
제한된 메모리 상황에서 캐싱 데이터를 저장하기 위핸, 지울 데이터 결정 알고리즘이다.

server.js에서 lruCache 인스턴스를 만들어주고, 얼마만의 시간동안 얼마만큼의 페이지를 캐싱할지 정해준다.  
url에 따라 캐싱하도록 조건부로 코드를 짜준다.

<br>

### renderToNodeStream 사용해보기

이전까지는 renderToString 함수를 썼었다. renderToString으로 컴포넌트를 string으로 바꾼 후  
`<div id='root'></div>`안에 넣어줬었다.  
renderToString은 모든 렌더링 과정 후에 결과를 반환하지만, renderToNodeStream은 바로 스트림 객체를 반환한다.  
스트림은 노드 뿐 아니라 다른 언어에도 존재하는 데이터 컬렉션으로 크기가 큰 데이터를 다룰 때 유용.  
데이터를 쪼개 청크 단위로 전달하기에 데이터가 완전히 준비되지 않아도 전송 시작이 가능한다.  
읽기전용, 쓰기전용, 양방향 스트림이 존재한다.  
양방향은 스트림을 연결하기 위해 쓰이는 경우가 많다.  
이를 통해 renderToString을 대체할 수 있고, 캐싱도 가능하다.

실제로 코드를 쳐야 하는 내용이 많아, 논리적인 흐름만 정리했다. 구체적인 코드는 책을 다시 참조.

<br>

## 넥스트 초급편

넥스트는 위에서 우리가 했던것들중 많은것을 대신 해준다.  
넥스트 자체적으로 서버를 띄워 SSR을 해준다.  
리액트의 메타 프레임워크 답게 기본으로 들어 있는 기능도 많다.

1. `/pages` 폴더 하위에 있는 컴포넌트들을 컴포넌트명대로 라우팅해준다.
2. 자체 라우팅 시스템에서 Link태그, useRouter를 지원한다.
3. `<Head />`를 통해 어디서든 메타 테그를 작성할 수 있다.
4. `_app`을 통해 모든 페이지에서 적용되야 하는 리액트 코드를 작성할 수 있다.
5. `_document`를 통해 서버측에서 HTML 요소를 추가할 수 있다.
6. cra와 다르게 웹팩 설정을 변경할 수 있다

### 웹팩 설정 변경

넥스트에선 정적 파일을 항상 static에 넣어둔다.  
여기서 캐싱을 최대로 활용하려면, 파일의 내용이 변경될때 파일의 경로도 변경되야 하는게 좋다.  
따라서 웹팩의 file-loader를 사용하면된다.

한번만 빌드하는 웹팩의 설정에선 객체를 리턴했고, 두번 이상 빌드할땐 함수를 통해 배열을 리턴했었는데, 기본으로 넥스트가 만들어주었던 웹팩 설정을 오버라이딩 하기 위해 함수를 통해 웹팩을 설정한다.

```jsx
module.exports = {
  webpack: config => {
    config.module.rules.push({
      //파일로더 설정
    });
    return config;
  },
};
```

<br>

### 서버에서 생성된 데이터 전달

책에서는 `getInitialProps`를 소개하고 있지만 현재는 `getServerSideProps`와 `getStaticProps` 사용이 권장된다.  
두 함수 모두 pages폴더 아래에 있는 페이지 컴포넌트에서 사용할 수 있고, 서버에서 렌더링 시켜서 넘겨줄 프롭스를 계산한다.  
함수의 인수로는 query와 html request등이 올 수 있어서 다양한 props 설정이 가능하다.  
api를 받아올수도 있고, query에 따른 작업을 해 줄 수도 있다.

getInitialProps의 경우, ssr도 가능하지만 클라이언트단에서도 실행 되어야 할수도 있기에 명확히 서버에서만 렌더링 될수 있도록 한 getServerSideProps가 보안상, 그리고 번들 사이즈 상 유리할수 있다.

[참고](https://develogger.kro.kr/blog/LKHcoding/133)

<br>

### 페이지 이동

Link 컴포넌트와 Router 객체를 통해서 한다.
다만 Link태그의 경우, href만 설정해주고 자식태그로 a태그를 만들어주어야 한다. (만들어주지 않은 경우 children이 text일 경우 자동으로 a태그를 생성한다고 한다.)  
만약 자식 a태그를 커스텀할 경우 Link에 passHref태그를 설정해주어야 한다. ([참고](https://f-dever-error-log.tistory.com/56))

[참고](https://uchanlee.dev/nextjs/Why-using-a-tag-in-nextjs-Link/)

<br>

### 에러 페이지 구현

`_error.js`를 만들어주면 된다.
getServerSideProps를 통해 res,err를 인수로 받을 수 있는데 이를 통해 statusCode등을 얻을 수 있다.

<br>

## 넥스트 고급편

코드 분할, 직접 띄운서버, styled-components 적용방법, 렌더링 결과 캐싱 및 미리 렌더링 방법을 알아보자.

<br>

### 페이지 공통 기능 구현

`_app.js`를 이용하자.  
\_app 컴포넌트는 `Components`와 `pageProps`를 props로 받는데, 컴포넌트에 pageProps를 넘겨주고, 그 위에 작성한 모든 jsx는 화면이 바뀌어도 언마운트 되지 않는다. 따라서 전역 상태 관리가 필요할 경우, app 컴포넌트를 이용할 수 도 있다.

<br>

### 코드 분할

넥스트는 기본으로, 페이지별 번들파일을 생성한다. (단 동적 임포트 사용시 해당 모듈은 별로의 파일로 분리, 여러페이지에서 사용하는 공통 모듈도 분리된다.)

1. 동적임포트를 사용해 조건부로 모듈을 불러온다.  
   조건부로 import 코드를 작성, then으로 이어받아 원하는 행동을 작성한다.
2. `getInitialProps`를 통한 동적 임포트는, 서버단에서 처리가 되기에 별도의 모듈로 빠지지 않는다.
3. 웹팩의 splitChunks 설정을 통해, 여러페이지에서 공통으로 사용되는 모듈을 별도의 번들파일로 분리한다. 코드 변경에 따른 캐시 무효화를 최소화 하는 방향으로 설계되어있다.

<br>

### 웹 서버 직접 띄우기

Next에 내장된 서버는 SSR 결과 캐시가 안되지만, 직접 띄운 서버에서는 처리할 수 있다.  
웹팩으로만 만들었을때 처럼, `express`를 설치하고 `server.js`를 만든다.

```jsx
//server.js

const express = require('express');
const next = require('next');

const port = 3000;
const isDev = precess.env.NODE_ENV !== 'production';
const app = next({isDev});
const handle = app.getRequestHandler();

app.prepare().then(()=>{
  const server = express();

  server.get('page/:id', (req,res)=>{
    res.redirect(`/pages${req.params.id}`);
  };
  server.get('*',(req,res)=>{
    return handle(req,res);
  })

  server.listen(port,err=>{
    if(err)throw err;
    console.log(`ready on http://localhost:${port}`)
  }
})

```

page/:id를 리다이렉트 해주는 부분 때문에 서버를 띄워준것이다.

<br>

## 렌더링 캐싱

전에 써봤던 `lruCache`라이브러리를 통해 server.js에서 캐싱을 해준다.

## 미리 렌더링

### 참고

[공식문서](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props)를 보면 진짜로 꼭 서버에서 데이터 패치를 해야하는 경우가 아니라면 getServerSideProps는 쓰지 말라고 한다.  
getServerSideProps는 요청이 있을때마다 SSR을 하고, 추가 설정 없이는 결과가 CDN에 캐시되지 않기때문.

getInitialProps의 경우, 사용시 미리 렌더링이 되지 않고, 요청시에 렌더링 되기 때문에 \_app에 선언하는것은 금물이다.

<br>

### next export

1. next export 명령어를 cli에 사용해 전체 페이지를 미리 렌더링 시켜줄 수 있다.
2. next.config.js에서 exportPathMap을 설정해주면, 미리 쿼리 파라미터 정보를 매핑해줄 수 있다.
3. 웹팩 파일을 수정해 정적페이지와 동적 페이지를 둘 다 서비스 할 수 있다.

## styled-components 사용

웹팩에서 css-in-js 사용시에도, 페이지가 깜빡이지 않도록 미리 설정을 해주었다.  
SSR시에도 동일하게 스타일 코드를 추출해 HTML에 삽입해주어야 한다.  
\_document를 이용하자.

[styled-components 공홈](https://styled-components.com/docs/advanced#nextjs)에도 잘 나와있다.

\_document만 수정할 경우, 서버에서 만들어진 해쉬값, 클라이언트에서 만들어진 해쉬값이 달라져 문제가 발생한다.  
따라서 바벨 플러그인을 하나 이용한다.

```json
{
  "presets": ["next/babel"],
  "plugins": ["babel-plugin-styled-components"]
}
```
