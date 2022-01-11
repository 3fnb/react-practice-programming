# 2장 ES6+를 품은 자바스크립트, 매력적인 언어가 되다

# 변수를 정의하는 키워드

이전가지 썼던 var, es6부터 나온 let, const를 살펴보자.

## var

1. 함수 레벨 스코프
2. 호이스팅
   스코프의 최상단으로 변수 선언문이 끌어 올려진다.
   변수 선언 전에도 변수의 참조가 가능하다. (undefined로 나옴)
3. 중복 선언이 가능하다.
   if문, for문 안에서 선언한 변수도 전역변수가 된다.
4. 암묵적인 전역 변수가 된다.
   window객체에 할당된다.

## let

1. 블록 스코프
2. 호이스팅
   let과 const도 호이스팅은 되지만, 변수 선언 이전에 참조가 불가능하다. (reference error)
   var는 선언과 초기화가 한번에 진행되어서 참조가 가능했다. (선언 단계에서 스코프-실행 컨텍스트의 렉시컬 환경-에 변수 식별자를 등록)
   let은 선언과 초기화가 분리되어 진행된다. 초기화 전에 접근하려 하면 reference error가 발생. 이 구간을
   TDZ(temporary dead zone)라고 부른다.
3. 중복 선언 금지

## const

상수를 선언하기 위해 사용된다.

1. 선언과 동시에 초기화 되어야 한다.
2. 재할당이 금지된다.
3. 변수 값을 변경할 수 없다. (불변은 아니다: 객체나 배열일 경우, 프로퍼티와 같은 안에 있는 값은 변경할 수 있다.)

# 객체와 배열

## 단축 속성명

shorthand property
짧게 줄인다!
뭘 짧게 줄일 수 있을까?
프로퍼티 이름과 값이 같을 때

## 계산된 속성명

computed property
속성명이 계산되어 있다?
변수를 속성명으로 쓸 수 있다!
단, dot natation은 안되고 bracket으로 써야 한다

## 전개 연산자

spread operator
뭘 전개할까? 뭘 퍼트릴까?
배열 안의 요소를 퍼트린다.

1. 배열을 풀어 여러 개의 파라미터로 넣을 때

```javascript
const numbers = [-1, 2, 3, 5];
//이전
Math.max.apply(null, numbers);
//spread operator 사용 후
Math.max(...numbers);
```

2. 배열과 객체를 복사할 때

```javascript
const arr1 = [1, 2, 3, 4];
const obj1 = { id: 1, name: 'wook' };

const arr2 = [...arr1];
const obj2 = [...obj1];
```

배열의 경우, spread operator 사용시 순서가 보장된다.

3. 배열끼리 객체끼리 병합, 분리

```javascript
const obj1 = { id: 1, name: 'wook' };
const obj2 = { hobby: 'javascript' };
const obj = { ...obj1, ...obj2 };
```

```javascript
const arr = [1, 2, 3];
const [first, ...arr] = arr;
```

중복된 속성명의 경우, 뒤에 오는것이 속성명의 값이 적용된다.

## 비구조화, 구조분해 할당

destructuring
기본값을 지정해 줄 수 있다.

### 배열

```javascript
const arr = [1, 2];
const [num1, num2] = arr;
console.log(num1, num2); // 1,2
```

서로 값을 바꿔야 할 때 간단히 이용할 수 있다.

```javascript
let num1 = 1;
let num2 = 2;
[num1, num2] = [num2, num1];
console.log(num1, num2); // 2,1
```

### 객체

1. 속성명말고 다른 변수에 할당 가능

```javascript
const obj = { age: 28, name: 'wook' };
const { age: age2021, name } = obj;
console.log(age2021); // 28
console.log(age); // reference error
```

2. 함수를 이용해서도 기본값 지정 가능
   단, 기본값으로 쓰일 때만 실행된다. (기본값이 쓸모없을경우는 변수에 들어가는 값이 있을때 . .!)

### 심화

배열 안에 있는 객체들도 구조분해 할당, 기본값, 계산된 속성명을 쓸 수 있다.

# 함수

## 기본값 매개변수(default parameter)

매개변수에서 기본값을 지정해 주어서, 파라미터가 없을 경우 기본값을 사용 가능

## 나머지 매개변수(rest parameter)

매개변수에서의 나머지란?  
확실히 들어올 변수 말고, 나머지로 자리를 남겨논 매개변수
매개변수의 개수가 변할 수 있을 때 유용  
(옛날에는 argument를 썼는데, 함수를 처음 보자마자 이게 나머지 매개변수가 있는지 없는지 알수 없고, 배열이 아니기 때문에 사용시 불편했다.)

## 명명된 매개변수 (named parameter)

default parameter와 구조분해 할당을 이용해 파이썬 처럼 명명된 매개변수를 이용할 수 있다.
단 함수 선언시 파라미터를 객체로 받을 수 있도록 해줘야 한다.

```javascript
  const showPasta = ({name:'알리오올리오', kcal:'640'}) => ({
    console.log(name,kcal);
  })
  shoPasta({name:'쉬림프로제'})
```

## 화살표 함수

```javascript
const functionName1 = (a, b) => {
  return a + b;
};
const functionName2 = (a, b) => a + b; // 한 줄로 리턴이 끝날 경우 중괄호와 같이 생략 가능. 중괄호가 있다면 return 키워드 필수
const functionName3 = a => a + 1; // 매개변수 하나일 경우 괄호 생략가능
const functionName4 = a => ({ name: a }); // 객체로 반환될 경우 소괄호로 감싸야함
```

화살표 함수는 this와 arguments를 바인딩하지 않는다.
일반함수는 객체 안에서 생성된 메소드라고 해도 쓰일때 무조건 this를 바인딩 한다. `객체.메소드` 형식일때 this는 `객체`이지만 그냥 `메소드`만 쓸 경우 전역객체를 this로 취급한다.

# 비동기 프로그래밍

## Promise

프로미스는 객체다. 비동기 상태를 값으로 다룰 수 있는.  
프로미스는 3가지 상태로 항상 존재한다.

1. pending: 대기중. 결과를 기다리는중
2. fulfilled: 이행됨. 비동기처리 정상 종료 및 결과값 가지고 있음.
3. rejected: 비동기처리가 비정상으로 종료

fulfilled와 rejected를 합쳐 settled 처리됨 상태라고 부른다. 이 settled가 되면 다른 상태로 바뀌지 않는다.

### 프로미스 생성

new 생성자를 붙여 만들 수 있다. 프로미스 안에는 콜백함수 2개를 인자로 받는다.
`const p1 = new Promise((res,rej)=> {return res(data) })`
첫번째 인자로 오는 함수는 정상적으로 비동기처리가 완료 되었을 때의 동작을
두번째 인자는 비정상 종료시의 에러를 나타낸다.
특이한것은, 프로미스 객체가 리턴하는것은 항상 프로미스 객체인것.

스태틱 메소드를 통해 settled 상태인 프로미스를 바로 생성할 수도 있다.
`const p2 = Promise.reject('에러메세지')`
`const p3 = Promise.resolve(param)`

### 프로미스 이용

프로미스 객체는 프로미스를 반환하면 . . . 어떻게 그 값을 이용할까 ?
바로 `then`을 사용!  
`then`은 또 프로미스를 반환해서, 연속적인 사용이 가능하다.  
`then`에는 프로미스와 같이 인자로 콜백함수 2개를 받는데, 두번째 인자로 에러 핸들링 하는것 보다 `catch` 메소드를 활용하는것이 직관적이다.
`finally`메소드는 프로미스가 이행됨,거부됨과 관계 없이 항상 호출되는 메서드이다.

### 프로미스 활용

1. Promise.all
   그냥 비동기로 코드 두개 써놓으면 되는데 왜 쓸까?
   Promise.all은 입력된 프로미스가 모두 처리됨 상태가 되야 처리됨 상태가 된다. 하나라도 실패하면 안되는데, 병렬로 처리하고 싶을 때 쓴다.

```javascript
Promise.all([reqData1(), reqDAta2()]).then(([data1, data2]) => {
  console.log(data1, data2);
});
```

2. Promise.race
   입력된 프로미스중 가장 빨리 처리된 프로미스만 반환한다.

3. 프로미스를 이용한 데이터 캐싱
   프로미스는 처리됨 상태가 되면 그 상태를 유지하는데, 이걸 이용해 캐싱을 구현할 수 있다.

```javascript
let cachedPromise;
const getDate = () => {
  const cachedPromise = cachedPromise || requestDataAPI();
  return cachedPromise;
};
```

### 신경쓸 만한 점

1. 화살표 함수로 쓸 경우가 많은데 이때 return 키워드 빼먹지 않도록
2. 프로미스는 불변객체로 변하지 않는다 . . . ! then 안에서 작업하기
3. 중첩해서 사용하는 프로미스는 콜백지옥과 같다
4. 동기코드 예외처리도 신경쓰기. catch메소드로만 에러를 잡는다면 동기코드를 비동기에 포함시키는것도 고려해봐라

## async와 await

프로미스보다 가독성이 좋을 수 있도록 만들어 주었다.

1. async await는 프로미스를 반환한다.
2. await는 async 안에서만 쓰기
3. 예외 처리는 try - catch로

# 템플릿 리터럴

백틱을 이용해 문자열을 동적으로 만들어준다.
변수는 `${}`를 사용해 쓸 수 있다.

## 태그된 템플릿 리터럴

스타일드 컴포넌트에선 백틱 안에 css를 작성한다.
템플릿 리터럴을 통해 함수의 매개변수를 전달한 것이다.
태그된 템플릿 리터럴은 괄호를 대신해 함수에 매개변수를 전달할 수 있다.

# 제너레이터

제너레이터는 함수의 실행을 중간에 멈추고, 또 재개할 수 있는 특수한 함수이다.

1. **제너레이터는 함수 호출자에게 함수 실행 제어권을 양도 할 수 있다.**  
   일반적인 함수들은 한번 실행하면 제어권이 함수에게 넘어가 조작을 할수 없는데, **제너레이터는 함수 호출자에게 양도(yield)할 수 있다.**

2. **제너레이터는 함수 호출자와 함수 상태를 주고받을 수 있다. (협업 멀티태스킹)**  
   일반적인 함수는 함수 실행중 함수 외부에서 내부로 값을 전달할 수 없다.
   제너레이터 함수는 가능하다.
   함수 호출자에게 상태를 전달할 수 있고, 함수 호출자로부터 상태를 전달받을 수 있다.

3. **제너레이터 함수는 제너레이터 객체를 반환한다.**  
   제너레이터 함수 호출 시 코드를 실행하지 않고 제너레이터 객체를 반환한다.

## 제너레이터 함수의 정의

function\* 키워드로 선언하고, 하나 이상의 yield 표현식을 포함한다. 화살표 함수로는 선언할 수 없다.

## 제너레이터 객체

제너레이터 객체는 이터러블 이면서, 이터레이터 이다.  
이터러블: Symbol.iterator 메소드를 상속받는다.
이터레이터: next 메소드를 소유한다. (next는 value와 done 프로퍼티를 갖는 객체를 반환하는 메소드)

## 제너레이터로 일시중지 및 재개하기

제너레이터는 `yield`와 `next`를 통해 실행 및 중지를 할 수 있다. <br><br>
제너레이터 함수 호출시 반환하는 제너레이터 객체는 `next` 메소드를 갖는데, 이 `next` 메소드를 호출 시 제너레이터 함수의 코드 블럭을 실행한다. 끝까지 실행하는것은 아니고 yield 표현식까지 실행한다.
<br><br>
`yield` 키워드는 제너레이터 함수의 실행을 중지, yield 키워드 뒤에 오는 (오른쪽에 있는) 표현식의 평과 결과를 반환한다.(제너레이터 함수 호출자에게)

```javascript
function* f1() {
  yield 10;
  yield 20;
  return 'finished';
}
const gen = f1();
//위에서 말한 제너레이터 함수 호출자. 제너레이터 객체를 반환한다.

console.log(gen.next());
//처음 next 호출 시 첫 번째 yield 표현식까지 실행되고 일시중지
// next 메소드가 반환하는것은  객체 {value: 10 ,done: false}
console.log(gen.next()); // {value:20,done:false}
console.log(gen.next()); // {value:undefined,done:true}
```

## next메소드에 인수 전달

next 메소드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당!
주의! next의 인수가 전해지는 것이지, yield 표현식의 평가 결과가 할당 되는것이 아님!!!
`const x = yield 1;` 에서 x는 1이 아니라 next의 인수 ! ! !

```javascript
function* minsu() {
  const myMsg = ['안녕 난 미눗', '만반잘부', '낼영화보실?'];
  for (const msg of myMsg) {
    console.log('수지 :', yield msg);
    // yield msg에는 next() 최초 실행 후 next()안에 인수로 들어오는 값이 들어간다
  }
}

function suji() {
  const myMst = ['', '안녕 난 수지', '그래반갑'];
  //처음에 전달하는 msg는 무시되기에 빈 스트링을 넣어놓은것이다. 왜냐하면 yield만 보면 멈춰버려서
  const gen = minsu();
  for (const msg of myMst) {
    console.log('민수:', gen.next(msg));
    // gen.next()가 실행되면, 제너레이터의 yield 오른쪽에 있는 값이 들어온다
  }
}
suji();
```

참고
자바스크립트 딥 다이브
