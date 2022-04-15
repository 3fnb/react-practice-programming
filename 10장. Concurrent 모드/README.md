## concurrent모드란?

concurrent모드 이전에는 렌더링을 시작하면 중간에 멈출수 없어서 유저의 키보드나 마우스 이벤트에 반응하지 못했다. 이를 해결하기 위한 방법을 운영체제의 선점형 멀티 태스킹 개념에서 찾았다. 운영체제는 CPU를 하나의 프로그램이 독점하지 않도록 일정시간이 지나면 다른 프로그램을 실행시켜주고 이후 다시 돌아와 기존의 프로그램 처리를 완료한다.

이와 마찬가지로 concurrent모드에서는 렌더링을 여러 개의 작업으로 나눠서, 실행 중인 작업을 중단하거나 중단된 작업을 재개할 수 있다. 실행 중인 작업의 중단 판단 기준은 다음 두가지이다.

- 작업이 일정 시간을 초과할 경우
- 현재 작업보다 우선순위가 높은 작업이 들어올 경우

<br>

## 작업 우선순위를 통한 concurrent모드

### 우선순위 결정 방식

1. 개발자가 직접 우선순위를 입력
   → 우선순위가 높은 함수, 낮은 함수 별도로 사용하는 방식
2. 이벤트별로 리액트가 자동으로 우선순위 결정
   → onKeyDown > onMouseOver
3. 화면에 보이지 않는 영역 우선순위 낮게
   → 화면에 보이는요소 렌더링 > 화면에 보이지 않는 요소 미리 렌더링

렌더링을 우선순위에 따라 중단하고 재개하는 시스템을 구현하기 위해서 리액트에서는 버전관리 시스템의 `리베이스(rebase)` 전략을 사용한다.

<br>

## React 18 새롭게 추가된 렌더링 관련 특징들

### Automatic Batching

리액트는 렌더링 성능 개선을 위해 16ms 이내에 요청되는 스태이트 값 업데이트는 한 번에 묶어서 처리하는 배치(batch)단위 처리를 지원한다. 하지만 이는 리액트 이벤트 핸들러 내부에서 실행될 경우에만 적용되고, `promise`, `fetch`, `setTimeout` 등의 기본 내장 API함수 내부에서는 적용되지 았았었다. 그러나 리액트18 부터는 이벤트핸들러 함수 내부가 아니어도 배치단위 렌더링이 자동으로 적용된다고 한다!

```jsx
// Before: only React events were batched.
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will render twice, once for each state update (no batching)
}, 1000);

// After: updates inside of timeouts, promises,
// native event handlers or any other event are batched.`
setTimeout(() => {
  setCount(c => c + 1);
  setFlag(f => !f);
  // React will only re-render once at the end (that's batching!)
}, 1000);
```

<br>

### Transitions

UI상태 업데이트에는 2가지 종류가 존재한다.

- **Urgent updates** : 클릭 및 입력에 따라 빠르게 변해야하는 업데이트
- **Transition updates** : 페이지 전환과 같이 천천히 크게 한 번 일어나는 업데이트

트랜지션을 사용하면 Transition updates의 렌더링이 진행되는 중에 유저에 의해서 Urgent updates가 발생한다면 즉시 Transition updates를 중단하고, 짧게 끝나는 렌더링을 우선 진행한다.

```jsx
import { startTransition } from 'react'; // useTransition 훅도 사용가능!

// Urgent: Show what was typed
setInputValue(input);

// Mark any state updates inside as transitions
startTransition(() => {
  // Transition: Show the results
  setSearchQuery(input);
});
```

<br>

### Suspense

서스펜스는 해당 리액트요소 내부의 컴포넌트들이 화면에 보여질 준비가 모두 완료될 때까지 `fallback` 속성에 주입된 컴포넌트를 화면에 보여준다. 이는 기존에 단축평가와 함께 `isLoading` 등을 덕지덕지 붙였던 부분을 제거해주는 효과가 예상된다.

```jsx
<Suspense fallback={<Spinner />}>
  <Comments />
</Suspense>

// 응용
function handleClick() {
  startTransition(() => { // 앞에 설명한 transition과의 조합이 좋다.
    setTab('comments');
  });
}

...

// Panel, Comments 모두 준비될 때까지 Spinner를 보여준다.
<div>
  {showComments && (
    <Suspense fallback={<Spinner />}>
      <Panel>
        <Comments />
      </Panel>
   </Suspense>
 )}
</div>
```
