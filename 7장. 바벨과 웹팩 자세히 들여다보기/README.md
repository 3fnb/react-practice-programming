## 바벨과 웹팩

웹개발의 필수템이다. CRA, next.js에 기본적으로 바벨과 웹팩이 탑제되어있으며, 자세한 사용법을 안다면 툴체인을 사용하지 않고도 바벨과 웹팩만으로 기본적인 개발환경 구축을 할 수 있다. 바벨과 웹팩을 이해해야 Jest, StoryBook, 서버사이드렌더링을 깊이있게 이해하고 사용할 수 있다.

<br/>

## 바벨을 실행하는 다양한 방법

- @babel/cli 로 실행하기
- 웹팩에서 @babel-loader로 실행하기 → 웹팩에서!
- @babel/core 를 직접실행하기
- @babel/register로 실행하기

@babel/register를 사용하면 노드에서 require가 실행될때 동적으로 바벨이 실행된다. 하지만 리액트에서 자주사용하지 않으므로 위 에 3가지 방법만 살펴봄

<br/>

### @babel/cli 사용하기

```bash
npx babel src/code.js
--presets=@babel/preset-react
--plugins=@babel/plugin-transform-template-literals,@babel/plugin-transform-arrow-functions
```

<br/>

### @babel/core 직접 이용하기

- 기본사용

```jsx
// runBabel.js

const babel = require('@babel/core');
const fs = require('fs');

const filename = './src/code.js';
const source = fs.readFileSync(filename, 'utf8');
const presets = ['@babel/preset-react'];
const plugins = [
  '@babel/plugin-transform-template-literals',
  '@babel/plugin-transform-arrow-functions',
];
const configFile = false;

const { code } = babel.transformAsync(source, {
  filename,
  presets,
  plugins,
  configFile,
});

console.log(code);

// 터미널
 node runBabel.js
```

<br/>

## 바벨 컴파일의 3단계

1. 파싱단계 → 입력된 코드에서 AST(abstract syntax tree)생성
2. 변환단계 → AST를 원하는 형태로 변환
3. 생성단계 → 변환된 AST를 다시 코드로 변환

<br/>

### AST를 재사용하는 바벨코드

```jsx
const babel = require('@babel/core');
const fs = require('fs');

const filename = './code.js';
const source = fs.readFileSync(filename, 'utf8');
const presets = ['@babel/preset-react'];

const { ast } = babel.transformFileSync(filename, {
  ast: true,
  code: false,
  presets,
  configFile: false,
});

const { code: code1 } = babel.transformFromAstSync(ast, source, {
  plugins: ['@babel/plugin-transform-template-literals'],
  configFile: false,
});

const { code: code2 } = babel.transformFromAstSync(ast, source, {
  filename,
  plugins: ['@babel/plugin-transform-arrow-functions'],
  configFile: false,
});

console.log('code1:\n', code1);
console.log('code2:\n', code2);
```

###

<br/>

## 바벨로 할 수 있는 것들!

### extends

→ 특정 config 상속받아 확장적용하기

### minify

→ 컴파일 결과 압축

### override

→ 특정 디렉토리 config 덮어쓰기

### 폴리필(poly fill)

→ 최신 자바스크립트 기능 사용하능하도록 코드변환

### @babel/preset-env

→ 실행환경정보(브라우저 시장 점유율, 브라우저 버전)에 따라 필요한 기능 주입

### babel-plugin-transform-remove-console

→ 전체 콘솔 한 방에 제거

### 커스텀 플러그인 제작

→ 커스텀 플러그인을 제작할 수 있으면 웬만한 코드 일괄 수정은 다 할 수 있다. 단, 커스텀 플러그인을 제작할 수 있으려면 AST에 대한 깊이있는 이해가 필요하다. 필요할 때 학습하자.

<br/>

## 웹팩(webpack)

웹팩은 모듈 번들러다. 자바스크립트 코드는 수많은 의존성 모듈의 결합으로 이루어지는데, 모듈별로 파일이 나누어져 있다면 클라이언트에 전송시 무지막지한 수의 파일을 전송하게 된다. 특히 SPA가 일반화 되면서 자바스크립트 파일 수가 급격하게 늘어났다고 한다. 이를 전송하기 간변하도록 하나의 자바스크립트 파일(번들이라고 부름)로 합칠 수 있는데 웹팩이 이를 가능하게한다.

<br/>

### 기본원리

웹팩을 설치하고 루트경로(프로젝트 폴더, src아님) 웹팩과 관련된 설정을 할 수 있는 `webpack.config.js`파일을 만든다. config파일에 우리가 원하는 설정을 해주고, 터미널에서 `npx webpack` 명령어를 실행한다. 정상적으로 실행이 완료되면 `dist`폴더(config에서 경로변경가능)에 번들링이 완료된 파일이 생성된 것을 확인가능하며, 이 자바스크립트 파일을 우리가 서비스 할 html과 연결시켜준다.

나 웹팩 때려죽어도 모르겠다!! 싶으면 \***\*webpack-cli generators\*\*** 사용해보자!

<br/>

## 웹팩으로 할 수 있는 것들!

### 자바스크립트 이외의 파일 불러오기

→ 웹팩을 통해서 babel-loader사용

### 이미지 파일 요청 횟수 줄이기

→ 이미지파일을 많이 사용하는 경우 용량이 작은 이미지 파일을 번들 안에 포함시켜 최초에 파일을 보낼때 같이 보낼 수 있는데 이때 너무 용량이 큰 사진이 같이 번들링 되어 자바스크립트가 실행되는 시간을 지연하는 경우가 발생하지 않도록 용량별로 구분해서 번들링이 가능하다. 그 때 url-loader를 사용.

### 자동으로 html파일도 같이 생성하기

→ html-webpack-plugin 사용

### 웹팩 실행시마다 dist폴더 자동 정리

→ clean-webpack-plugin 사용

### 특정 문자열을 원하는 문자열로 대체

→ 내장플러그인 DefinePlugin 사용

### 자주 사용되는 모듈 import 코드 일괄 추가

→ 내장플러그인 ProvidePlugin 사용

### 사용하지 않는 모듈들 번들에서 제거

→ 나무흔들기(Tree Shaking) 내장적용됨

→ 트리쉐이킹이 잘 적용되지 않는경우 주의

- 사용되는 모듈이 ESM이 아닌경우
- 사용하는 곳에서 ESM을 사용하지 않는경우
- 동적 임포트(dynamic import)를 사용하는 경우

### 코드 분할(code spliting)

→ 번들링을 하다보면 한번에 너무 많은 양의 코드를 번들로 만들어 전송하므로 초기 로딩이 지연될 수도 있다. 따라서 번들을 청크(chunk)단위로 분할하여 번들링 한 후, 해당 청크가 필요한 시점에 로딩하는 코드 레이지로딩(Lazy Loading)을 하는 구조를 만들 수 있다. 이때 코드분할(분할 번들링)이 활용된다.

### 코드분할된 파일을 미리 다운받아두거나 미리 실행해두기

→ prefetch, preload 설정 사용

### 커스텀 로더, 플러그인 제작

→ 위에 열거한 사항 외에도 바벨과 같이 커스텀 로더 및 플러그인을 제작할 수 있다.
