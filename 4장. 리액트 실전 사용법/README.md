# 4장. 리액트 실전 활용법

## 가독성과 생산성을 고려한 컴포넌트 작성법

좋은 리액트 코드란?

- 유지보수하기 쉬운코드
- 인터페이스를 쉽게 파악할 수 있는 코드

<br>

### 추천 컴포넌트 파일 작성법

1. types

- 가장 위에 두어 컴포넌트 속성값 파악이 용이하도록

2. component

- 매개변수 비구조화 할당 해주기

3. 상수변수

- 가능한 컴포넌트 외부에 정의하기 → 불필요한 재정의를 막을 수 있음

- 유사한 기능의 훅끼리 모으기
  → useEffect에 사용되는 useState 붙여놓기
- 코드 복잡도가 높아지면 커스텀훅으로 각 기능 분리하여 관리
- 타입정의(prop-types)
  → prop-types는 리액트 내장 정적타입 검사툴이었는데 15.5버전부터 외부라이브러리로 빠졌다.
  [https://ko.reactjs.org/docs/typechecking-with-proptypes.html](https://ko.reactjs.org/docs/typechecking-with-proptypes.html)
- 변수를 배열 혹은 객체로 사용할 거면 선언시 빈배열, 빈객체를 부여하기
  → 그래야 메서드 오류를 피할 수 있음.

<br>

### 조건부 렌더링

- 일반적으로 삼항연산자보다 &&가 가독성이 더 좋다.
- 단축평가 마스터하기
- && : 첫 거짓 or 마지막 값 반환
- || : 첫 참 or 마지막 값 반환
- 조건부 렌더링은 방법이 다양하므로 컨벤션으로 정해두면 좋다.
- 조건부 렌더링을 수정할 대는 리뷰어를 배려하는 마음을 가지자.

조건부 렌더링에 관심이 있다면 아래 글 참고 해보세요~

[[React] 조건부 렌더링 4가지 방법](https://pspace21.tistory.com/67)

<br>

### 프레젠테이션 , 컨테이너 컴포넌트 구분하기

댄 아브라모프가 제시한 컴포넌트 구분 관리법.

- 프레젠테이션 컴포넌트 : UI의 마크업, 스타일만 가지고 UI와 관련한 상태값만 가진다.
- 컨테이너 컴포넌트 : 데이터 가공, 리덕스관련 내용만 가지고 마크업, 스타일 코드가 없다.

이는 기존의 클래스형 컴포넌트를 사용할 시 컨테이너 컴포넌트가 프레젠테이션 컴포넌트를 감싸는 형식으로 사용되었다. 훅의 등장 이후 커스텀 훅 등으로도 데이터로직과 UI표현을 구분하는 기능을 구현할 수 있다.

- 자식 컴포넌트에서 부모의 데이터를 별도의 상탯값으로 관리하는 것은 안티패턴이다.

<br>

## useEffect 실전 활용법

### 의존성 배열 관리법

대부분의 문제점들은 의존성 배열을 잘못 하용하여 발생한다. 꼭 필요한 상황이 아니면 의존성배열에 값을 입력하지 않는 것이 좋다.

<br>

### 부수 효과 함수에서 API를 호출하는 경우

```jsx
export default function useUser(userId) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(
      `https://jsonplaceholder.typicode.com/comments?_page=${userId}&_limit=1`
    )
      .then(res => res.json())
      .then(([{ name, id }]) => setUser({ name, id }))
  }, [userId]);  // userId값이 변경될 때마다 부수효과함수가 실행됨.

	...
}
```

<br>

### 부수 효과 함수 안에서 async, await 사용하기

```jsx
export default function useUser(userId) {
  const [user, setUser] = useState(null);

  useEffect(() => {
	// 함수 정의하고
    async function fetchAndSetUser() {
      await fetch(
        `https://jsonplaceholder.typicode.com/comments?_page=${userId}&_limit=1`
      )
        .then(res => res.json())
        .then(([{ name, id }]) => setUser({ name, id }));
    }

  // 실행시켜줌
    fetchAndSetUser();
  }, [userId]);

  ...
}
```

<br>

### 재사용하는 함수를 useEffect내부에서 사용할 시

useEffect외부에서 정의된 함수를 내부에서 사용할 경우 의존성배열에 해당 함수를 넣어야하며 그렇게 되면 렌더될 때마다 함수가 재정의 되고, 그로인해 부수효과함수가 렌더링 할 때마다 호출된다. 이를 피하기 위해서는 useCallback같은 훅을 사용하여 재사용함수가 필요할 때마다 갱신되도록 만들어야한다.

<br>

### 의존성 배열 없애는 방법

의존성 배열을 가능하면 만들지 않는것이 좋다. 관리하는데 시간과 노력이 많이 소요되기 때문이다.

1. 부수효과 내부에서 if사용
   ⇒ 일단 항상 부수효과 함수는 호출하되 부수효과 내부에서 조건에 따라 함수를 호출한다.
2. 상태값 변경시 prev 사용
   ⇒ 이전상태값으로 다음 상태값이 변경 된다면 state를 직접 쓰지말고 prev를 활용하자.
3. useReducer사용
   ⇒ 상태값에 따라 다양한 종류의 상태값변경 연산로직이 존재하면 리듀서 사용하자.
4. useRef 사용
   ⇒ props로 함수를 받으면 함수변경이 없어도 렌더링 될때마다 부수효과함수를 호출하게되므로 `ref.current`안에 넣어두고 `ref.current()`로 호출한다. 단, 렌더링과 무관한 함수일 것!
