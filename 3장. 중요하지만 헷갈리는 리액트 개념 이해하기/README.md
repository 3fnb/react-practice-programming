## 상탯값과 속성값으로 관리하는 UI 데이터

- 리액트를 사용하지 않은 코드는 로직과 UI 코드가 복잡하게 얽혀 있어 가독성이 낮지만, 리액트를 사용하면 선언형 프로그래밍으로 코드를 작성하여 추상화 단계가 높으며 비즈니스 로직에 좀 더 집중할 수 있다. 
- 화면의 갱신을 위해 UI 데이터는 반드시 상탯값(해당 컴포넌트가 관리하는 데이터)과 속성값(부모 컴포넌트로부터 전달받는 데이터)로 관리해야 한다. 
  * 자식 컴포넌트는 부모 컴포넌트가 렌더링될 때 같이 렌더링된다. 내려받는 속성값이 변경될 때만 렌더링되길 바란다면 React.memo를 사용할 수 있다. 
  ```
  function Title(props) {
    return <div>{props.title}</div>;
  }
  export default React.memo(Title);
  ```
- 같은 컴포넌트를 두 번 사용할 경우 두 개의 상탯값이 따로 관리된다.
- 속성값은 불변 변수이고 상탯값은 직접 수정이 가능하다. 하지만 리렌더링을 위해 상탯값도 상탯값 변경 함수를 통해 불변 변수로 관리하는 것이 좋다. 
- 컴포넌트 함수는 다양한 값을 반환할 수 있다. 
  * 배열을 반환할 수 있으며 각 리액트 요소는 key 속성값을 갖고 있어야 한다. 
  * 프래그먼트 내부 리액트 요소에 key 속성값을 부여하지 않아도 되므로 배열보다 편리하게 사용할 수 있다. 
  * 프래그먼트는 바벨을 이용해 <></>로 축약하여 사용할 수 있다.
  * 리액트 포털(portal)을 사용해 컴포넌트의 현재 위치와는 상관 없이 특정 돔 요소에 렌더링할 수 있다. 
  ```
  function MyComponet({title}) {
    return title.length > 0 && <p>{title}</p>;
  }
  ```
  
## 리액트 요소와 가상 돔

- 리액트는 렌더링 성능을 위해 가상 돔을 활용하는데, 메모리에 가상 돔을 올려 놓고 이전과 이후의 가상 돔을 비교해서 변경된 부분만 실제 돔에 반영한다. 리액트 요소는 불젼 객체이기 때문에 속성값을 변경할 수 없고, 변경된 부분만 비교해 렌더링한다. 
- 리액트 요소가 돔 요소로 만들어지는 과정
  * 하나의 화면을 표현하기 위해 여러 개의 리액트 요소가 트리 구조로 구성된다. 트리는 시간에 따라 변화하는 화면의 한순간을 나타낸다.
  * 데이터 변경에 의한 화면 업데이트는 렌더 단계(실제 돔에 반영할 변경 사항을 파악하는 단계)와 커밋 단계(파악된 변경 사항을 실제 돔에 반영하는 단계)를 거친다.
  * 렌더 단계에서 실제 돔의 변경 사항을 최소화하기 위해 렌더링을 할 때마다 가상 돔을 만들고 이전의 가상 돔과 비교한다.
  * 리액트 요소 트리가 실제 돔으로 만들어지기 위해서는 모든 리액트 요소의 type 속성값이 HTML 태그로 변환할 수 있는 문자열이어야 한다. 따라서 모든 컴포넌트 함수가 호출되어야 한다. 

## 리액트 훅 기초 익히기

- 컴포넌트에 기능을 추가할 때 사용하며 함수형 컴포넌트에서 상태값을 사용할 수 있고 자식 요소에 접근할 수도 있다. 

### useState: 상탯값 변경 함수
- 상탯값 변경 함수로 입력된 함수는 자신이 호출되기 직전의 상탯값을 매개변수로 받는다. 
- 상탯값 변경 함수는 비동기로 처리되지만 그 순서가 보장된다. 
```
function myComponent() {
  const [count, setCount] = useState(0);
  const onClick = () => {
    setCount(prev => prev + 1); //1
    setCount(prev => prev + 1); //2
  };
};
```
- 기존 상탯값과 새로 입력된 값을 병합하는 setState메서드와 달리 useState는 이전 상탯값을 덮어쓰기 때문에 spread syntax 등을 활용해야 한다. 

### useEffet: 부수 효과 처리하기
- 함수 실행 시 함수 외부의 상태를 변경하는 연산을 부수 효과라고 한다. 
- 렌더링 결과가 실제 돔에 반영된 후 비동기로 호출된다. 
- 훅 사용시 규칙
  * 하나의 컴포넌트에서 훅을 호출하는 순서는 항상 같아야 한다. 
  * 훅은 함수형 컴포넌트 또는 커스텀 훅 안에서만 호출되아야 한다. 
  
## 콘텍스트 API로 데이터 전달하기

콘텍스트 API로 중첩 구조가 복잡한 상황에서 중간 컴포넌트가 개입하지 않고도 쉽게 데이터를 전달할 수 있다. 

1. createContext 함수를 호출한다. 
2. 상위 컴포넌트에서 Provider 컴포넌트를 이용해 데이터를 전달한다. 
3. 하위 컴포넌트에서 Consumer 컴포넌트를 이용해 데이터를 사용한다. *상탯값 변경 함수를 전달해 하위 컴포넌트에서도 콘텍스트 데이터 수정을 할 수 있다. 

* 주의할 점
1. 불필요한 렌더링이 발생할 수 있다. 콘텍스트 데이터를 객체로 전달하면 렌더링될 때마다 새로운 객체가 새성되기 때문에 데이터 전체를 전달해야 한다. 
2. Proider 컴포넌트로 Consumer를 꼭 감싸줘야 한다. 

간단한 예시 https://codepen.io/darylthornhill/pen/pooXMwq

## ref 속성값으로 자식 요소에 접근하기

돔 요소에 직접 접근해야 할 때 ref 속성값을 이용해 자식요소에 접근할 수 있다. useRef 훅이 반환하는 ref 객체를 이용해 자식 요소에 접근할 수 있다. 자식 요소의 ref 속성 값에 ref 객체를 입력한 뒤 해당 돔 요소 혹은 컴포넌트가 생성되면 ref 객체의 current 속성을 이용해 자식 요소에 접근할 수 있다.

### ref 값의 활용
- forwardRef 함수로 부모 컴포넌트엑서 넘어온 ref 속성값 직접 처리하기
 ```
 const FancyButton = React.forwardRef((props, ref) => (
   <button ref={ref} className="FancyButton">
     {props.children}
   </button>
 ));

 // 이제 DOM 버튼으로 ref를 작접 받을 수 있습니다.
 const ref = React.createRef();
 <FancyButton ref={ref}>Click me!</FancyButton>;
 ```
 코드 출처: https://ko.reactjs.org/docs/forwarding-refs.html

* 주의할 점
컴포넌트가 생성된 이후라도 조건부 렌더링을 하는 경우 ref 속성값을 입력한 요소가 존재하지 않을 수 있기 때문에 current 속성 존재 여부를 검사하는 코드가 필요하니 유의해야 한다. 

## 리액트 내장 훅 

### useContext: Consumer컴포넌트 없이 콘텍스트 사용
```
function ChildComponent() {
 const user = useContext(UserContext);
}
```
useContext를 사용하면 Consumer를 사용하지 않고도 context 데이터를 사용할 수 있게 된다.

### useref: 렌더링과 무관한 값 저장
컴포넌트 내부에서 생성되는 값 중 렌더링과 무관한 값을 저장할 때 사용한다. 
예를 들어, useRef 훅을 이용해서 이전 상탯값을 저장할 수 있다. 
```
function Profile() {
 const [age, setAge] = useState(20);
 const prevAgeRef = useRef(20);
 useEffect(
  () => {
   prevAgeRef.current = age; 
  }, [age],
 )
 
 const prevAge = prevAgeRef.current;
 return (<></>)
}
```
age 값이 변경되면 그 값을 prevAgeRef에 저장한다. 

### useMemo & useCallback: 메모이제이션 훅
이전 값을 기억해 성능을 최적화하는 용도로 사용된다. 

- useMemo: 계산량이 많은 함수의 반환값을 재활용한다. 
```
function MyComponent({v1, v2}) {
 const value = useMemo(() => runExpensiveJob(v1, v2), [v1, v2]);
 return (<p>{value}</p>);
}
```
의존성 배열이 변경되지 않으면 이전에 계산된 값을 기억하고 사용한다. 

- useCallback: 함수를 재활용하여 불필요한 렌더링을 막는다.
```
function Profile() {
 const [name, setName] = useState('');
 const [age, setAge] = useState(0);
 const onSave = useCallback(() => saveToServer(name, age), [name, age]);
 return (<UserEdit onSave={onSave} />)
}
```
의존성 배열이 변경되지 않으면 이전에 생성한 함수가 재사용된다. 

### useReducer: 컴포넌트의 상탯값 관리하기
상위 컴포넌트에서 트리 깊은 곳으로 이벤트 처리 함수를 쉽게 전달할 수 있다. 
```
const initialState = {count: 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```
코드 출처: https://ko.reactjs.org/docs/hooks-reference.html#usereducer

### useImperativeHandle: 부모 컴포넌트에서 접근 가능한 함수 구현
부모 컴포넌트는 ref 객체를 통해 클래스형 컴포넌트인 자식 컴포넌트의 메서드를 호출할 수 있는데, useImperativeHandle 훅을 이용하면 함수형에서도 메서드가 있는 것처럼 만들 수 있다. 
```
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```
코드 출처: https://ko.reactjs.org/docs/hooks-reference.html#usereducer

### useLayoutEffect
useEffect와 비슷하게 동작하지만 부수 효과 함수를 동기로 호출한다는 점이 다르다. 즉, 렌더링 결과가 돔에 반영된 직후에 호출된다. 이 훅에서 연산을 많이 하면 브라우저가 먹통이 될 수 있어 useEffect를 사용하는 것이 성능상 이점이 있다.

### useDebugValue
커스텀 훅의 내부 상태를 관찰할 수 있어 디버깅에 도움이 된다. 리액트 개발자 도구에서 확인할 수 있다. 
```
function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  // ...

  // Show a label in DevTools next to this Hook
  // e.g. "FriendStatus: Online"
  useDebugValue(isOnline ? 'Online' : 'Offline');

  return isOnline;
```
