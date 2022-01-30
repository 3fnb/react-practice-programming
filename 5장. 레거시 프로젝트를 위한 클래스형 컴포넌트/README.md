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
1. constructor(props)
  - 
## 클래스형 컴포넌트 기본 사용법
## 컴포넌트의 공통 기능 관리하기
## 클래스형 컴포넌트와 훅의 관계
## 클래스형 컴포넌트를 훅으로 변환하기
## 기존 클래스형 컴포넌트를 고려한 커스텀 훅 작성법
