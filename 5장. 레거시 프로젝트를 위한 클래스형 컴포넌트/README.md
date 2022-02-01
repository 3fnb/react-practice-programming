## 클래스형 컴포넌트의 생명 주기 메서드
모든 컴포넌트는 초기화 단계, 업데이트 단계, 소멸 단계를 거친다. 각 단계 속에서 호출되는 메서드를 생명 주기 메서드라고 한다. 
![lifecycle](https://cdn.filestackcontent.com/ApNH7030SAG1wAycdj3H)

#### 초기화 단계
- constructor()
- static getDerivedStateFromProps()
- render()
- componentDidMount()

#### 업데이트 단계
- static getDerivedFromProps()
- shouldComponentUpdate()
- render()
- getSnapshotBeforeUpdate()
- componentDidUpdate()

#### 소멸 단계
- componentWillUnmount()

#### 에러 발생
- static getDerivedStateFromError()
- componentDidCatch()

### 각 생명 주기 메서드 사용법
1. constructor
  - constructor(props)
  - constructor 내부에서 super 함수를 호출해야 constructor 메서드가 호출된다.
  - 상태값을 직접 할당할 수 있다.(다른 메서드에서는 setState 메서드 사용)
  - constructor 내부에서 호출되는 setState는 무시된다.
  - constructor를 사용하지 않고 같은 기능을 구현하기 위해 클래스 필드 문법을 사용할 수 있다. 
    ```
    class MyComponent extends React.Component {
        state={
          currentMovie: this.props.age < 10 ? '뽀로로' : '어벤져스',
        }
    }
    ```
2. getDerivedStateFromProps 
  - static getDerivedStateFromProps(props, state)
  - 속성값을 이용해 새로운 상탯값을 만들때 사용된다.
  - render 메서드가 호출되기 직전에 호출된다. 
  - 정적 메서드이기 때문에 함수 내부에서 this 객체에 접근할 수 없고, 오로지 속성값과 상탯값을 기반으로 새로운 상탯값을 만든다. 
  - 메모리 절약을 위해 이전 속성값을 props로 받지 않기 때문에 이전 속성값은 상탯값에 저장하여 관리해야 한다. 
  - 이전 속성값과 이후 속성값 모두에 의존적인 상탯값이 필요할 때 유용하며, 이 메서드가 필요한 경우는 그렇게 많지 않다. 

3. render
- 컴포넌트를 정의할 때 반드시 작성해야 한다. 
- 부수 효과(서버와 통신, 쿠키에 저장 등)를 발생시키면 안된다. 

4. ComponentDidMount 
- render 메서드의 첫 번째 반환값이 실제 돔에 반영된 직후 호출된다. 
- setState 메서드가 마운트 이후에만 동작하기 때문에 API 호출을 통해 데이터를 가져올 때 적합하다.
- constructor 메서드에서 API 호출 결과를 더 빨리 받을 수 있는 방법은 프로미스를 이용하는 것이다.(응답 속도에 민감함 경우에 사용)
  ```
  class MyComponent extends React.Component {
    constructor(props) {
      super(props);
      this.dataPromise = requestData();
    }
    componentDidMount () {
      this.dataPromise.then(data => this.setState({data}));
    }
  }
  ```
  
5. shouldComponentUpdate
- shouldComponentUpdate(nextProps, nextState)
- 성능 최적화를 위해 존재
- 불린 타입을 반환하며 true를 반환하면 render 메서드가 호출된다.
- 한 번의 변수 비교를 통해 불필요한 연산을 줄이는 효과가 있다.

6. getSnapshotBeforeUpdate
- getSnapshotBeforeUpdate(prevProps, prevState) => snapshot
- 렌더링 결과가 실제 돔에 반영되기 직전에 호출된다.
- 이전 돔 요소의 상탯값을 가져오기 좋다. 
- 이 메서드가 반환한 값은 componentDidUpdate 메서드의 세 번째 인자로 들어간다.

7. componentDidUpdate
- componentDidUpdate(prevProps, prevState, snapshot)
- 가상 돔이 실제 돔에 반영된 후 호출된다.
- 속성값이나 상탯값이 변경된 경우 API를 호출하는 용도로 사용되기도 한다. 

8. componentDidUnmount
- 소멸 단계에서 호출된다. 
- 끝나지 않은 네트워크 요청을 취소, 타이머 해제, 구독 해제 등의 작업을 처리하기 좋다. 

9. getDerivedStateFromError, componentDidCatch
- 생명주기 메서드에서 예외가 발생하면 두 메서드를 구현한 가장 가까운 부모 컴포넌트를 찾는다.
- static getDerivedFromError(error)는 에러 정보를 상탯값에 저장해서 화면에 나타내는 용도로 사용된다.
- componentDidCatch(error, info)는 에러 정보를 서버로 전송하는 용도로 사용된다.
- error는 에러 객체이며 info는 어떤 컴포넌트에서 예외가 발생했는지 알려준다. 
- 서버사이드 렌더링 시 에러가 발생해도 componentDidCatch 메서드는 호출되지 않는다. 
 
## 클래스형 컴포넌트 기본 사용법
### setState
- 상탯값을 병합한다. 
- 비동기로 상탯값을 변경한다. 
- 두 번째 매개변수는 처리가 끝났을 때 호출되는 콜백 함수다. 
- forceUpdate 메서드로 상탯값을 직접 수정할 수 있지만 불변값으로 관리하는 것이 좋다. 

### 클래스 필드를 이용해 이벤트 처리 메서드 작성하기
- 렌더링 성능과 가독성의 이점을 갖는다.
- 함수를 바인딩할 때마다 새로운 함수가 생성되기 때문에 render 메서드 내부에서 함수를 바인딩하면 성능에 좋지 않다. 성능을 위해 생성자 안에서 바인딩한다. 
  ```
  class MyComponent extends Component {
    constructor(props) {
      super(props);
      this.onClickInc = this.onClickInc.bind(this);
    }
    ...
    render() {
      return (
        <button onClick={this.onClickInc} />
      )
    }
  }
  ```
- 화살표 함수를 사용하는 경우 자동으로 바인딩 되며, 클래스 인스턴스가 생성될 때 한 번만 바인딩되므로 렌더링 성능에도 문제가 없다. 

### 생명 주기 메서드에서 컨텍스트 데이터 사용
- 클래스형 컴포넌트의 contextType 정적 멤버 변수에 컨텍스트 객체를 입력하면 클래스 내부에서 컨텍스트 데이터에 접근할 수 있다. 
  ```
  const ThemeContext = React.createContext('dark');
  
  class MyComponent extends React.Component {
    componentDidMount() {
      const theme = this.context;
      ...
    }
    ...
  }
  MyComponent.contextType = ThemeContext;
  ```
## 컴포넌트의 공통 기능 관리하기
- 클래스형 컴포넌트에서는 훅 대신 고차 컴포넌트와 렌더 속성값 패턴으로 공통 기능을 관리할 수 있다. 
### 고차 컴포넌트
- 고차 컴포넌트는 컴포넌트를 입력으로 받아서 컴포넌트를 출력해주는 함수다. 
## 클래스형 컴포넌트와 훅의 관계
## 클래스형 컴포넌트를 훅으로 변환하기
## 기존 클래스형 컴포넌트를 고려한 커스텀 훅 작성법
