# 타입스크립트란?

자바스크립트의 모든 기능을 포함하며 정적 타입을 지원하는 언어다. 정적 타입은 왜 필요하고 타입스크립트는 어떤 장점이 있을까? 

- 동적 타입 언어는 타입 오류가 런타임 시 발견되고, 정적 타입 언어는 타입 오류가 컴파일 시 발견된다.
- 변수를 선언할 때마다 타입을 고민해야 하기 때문에 진입 장벽이 높지만 코드의 양이 많을 때 리팩토링이 쉬워 생산성이 좋다.
- 타입스크립트 장점
    - 마이크로소프트에서 개발며 꾸준히 업데이트 된다.
    - 마이크로소프트에서 개발한 vscode와 합이 잘 맞는다.
    

# 여러 가지 타입

```jsx
const size: number = 1;
const isBig: boolean = false;
const msg: string = 'abc';

const values: number[] = [1,2];
const values2: Array<number> = [1,2];

const a: undefined = undefined;
const b: null = null;
```

* 문자열 리터럴과 숫자 리터럴을 타입으로 정의할 수도 있다. 

  ```jsx
  let a: 1 | 2 | 3;
  a = 4 //타입 에러

  let b: '다빈' | '욱창';
  b = '시원스쿨' //타입에러
  ```

* any 타입에는 숫자, 문자열뿐 아니라 함수도 입력될 수 있다. 하지만 타입스크립트를 사용하는 의미가 퇴색되므로 피하는게 좋다.

* 함수 타입

  ```jsx
  // void: 아무 값도 반환하지 않는 경우

  function f1(): void {
    console.log('hello')
  }

  // never: 항상 예외가 발생해서 비정상적으로 종료되거나 무한 루프 때문에 종료되지 않는 경우

  function f2(): never {
    throw new Error('some error');
  }
  ```

* object 타입

  ```jsx
  let v: object;
  v = {name : '다빈'};
  console.log(v.name) // 타입 에러
  ```

  * interface를 사용해 속성 정보를 포함해 정의해야 한다. 밑으로 내려주세요! 

* 교차 타입(교집합)과 유니온 타입(합집합)

  ```jsx
  // 교차 타입 &
  // 유니온 타입 |

  let v1: (1| 2 | 3) & (3 | 4);
  v1 = 4; //타입 에러
  ```

* type 키워드로 별칭을 부여할 수 있다. 

  ```jsx
  type Width: number | null;

  let width: Width;

  width = 2;
  width = '2'; //타입 에러
  ```

* 열거형 타입은 enum을 사용해 정의한다. 
  * 각 원소는 값이나 타입으로 사용될 수 있다. 
  * 열거형 타입은 객체로 존재하기 때문에 해당 객체를 런타임에 사용할 수 있다. 

  ```jsx
  enum Food {
    Pasta,
    Salad,
    Pizza,
  }

  const v1: Food = Food.Pasta; //Pasta를 값으로 사용
  const v2: Food.Pasta | Food.Pizza = Food.Pizza; //Pasta를 타입으로 사용


  // 명시적으로 원소의 값 입력하기
  enum Food {
    Pasta,  //첫번째 원소에 값을 할당하지 않으면 자동으로 0이 할당된다. 
    Salad = 5,
    Pizza,  //값을 할당한 원소 다음 원소는 자동으로 이전 원소 + 1 이 할당된다.

  }

  console.log(Food[5]) //Pasta
  
  // 각 원소의 값과 이름이 양방향으로 매핑되어 값을 이용해 이름을 가져올 수도 있다. 
  // 원소에 문자열을 할당하는 경우에는 단방향으로 매핑되어 값을 이용해 이름을 가져올 수 없다. 참고! 
  ```

  * 열거형 타입은 컴파일 후에도 남아 있어 번들 파일의 크기가 커진다. 상수(const) 열거형 타입을 사용하면 컴파일 결과에 객체를 남기지 않을 수 있다. 

    ```jsx
    const enum Food {
      Pasta,
      Salad,
      Pizza,
    }

    const pasta: Food = Food.Pasta;

    //컴파일 결과
    const pasta = 0;
    
    // 하지만 열거형 타입을 상수로 정의하면 열거형 타입의 객체를 사용할 수 없으니 주의하자
    ```

* 함수타입

```jsx
// 변수를 함수 타입으로 정의하는 방법
const getInfoText: (name: string, age?: number) => function(name, age) {
}

function getInfoText(name: string, age: number): string {
}
```
선택 매개변수 오른쪽에 필수 매개변수를 정의하면 컴파일 에러가 발생한다. 
이 경우에는 undefined를 사용한다. 
```jsx
function getInfoText(
	name: string,
	language: string | undefined,
	age: number,
): string {
 //...
}

getInfoText('mike', undefined, 23);
```
기본값이 있으면 선택 매개변수가 된다. 
```jsx
function getInfoText(
	name: string,
	age: number = 15,
): string {
 //...
}
```
나머지 매개변수는 배열로 정의한다. 
```jsx
function getInfoText(name: string, ...rest: string[]): string {
	//...
}
```
함수의 this 타입을 정의하지 않으면 기본적으로 any 타입이 사용된다. 
```jsx
function getParam(this: string, index: number): string {
	const params = this.splt(','); //타입에러
}
```
원시 타입에 메서드 추가하려면 인터페이스를 사용한다. 
```jsx
interface String {
	getPram(this: string, index: number): string;
}

String.prototype.getParam = getPram;

// 이제 문자열에 등록된 getParam 메서드를 호출할 수 있다. 
```
함수 오버로드: 여러개의 타입 정의하기
```jsx
// 1 매개변수와 반환 타입의 모든 가능한 조합을 정의한 뒤 실제 구현하는 쪽에서 정의한 타입은 함수 오버로드의 타입 목록에서 제외된다. 

function add(x: number, y: number): number;
function add(x: string, y: string): string;
function add(x: number | string, y: number | string): number | string {
//...
}

// 2 명명된 매개변수
interface Param {
	name: string;
	age?: number;
	language?: string;
}
function getInfoText({ name, age = 15, language }: Param): string {
}
```

# 인터페이스

* 객체 타입 정의하기
    
  ```jsx
  interface Person {
    name: string;
    age?: number;
  }
  const p1: Person = {name: 'mike', age: 23 };

  // ?는 선택속성

  // 하지만 undefined로 정의하는 경우 해당 속성을 입력해야 타입 에러가 발생하지 않는다. 
  interface Person {
    name: string;
    age: number | undefined;
  }
  const p1: Person = {name: 'mike' }; //타입에러

  // 읽기 전용 속성: 변수를 정의하는 시점만 값을 할당할 수 있으며 이후에는 수정이 불가능하다. 
  interface Person {
    readonly name: string;
    age: number | undefined;
  }

  // 정의되지 않은 속성값을 할당하는 경우 타입 에러가 난다. 
  ```
    
* 인터페이스로 정의하는 인덱스 타입
  a. 속성 이름을 구체적으로 정의하지 않고 타입만 정의하는 것을 인덱스 타입이라 한다. 

    ```jsx
    interface Person {
      readonly name: string;
      age: number;
      [key: string]: string | number;
    }

    // 여러 개의 인덱스를 정의하는 경우
    interface YearPriceMap {
      [key: number]: number;
      [year: string]: string | number;
    }
    ```

  b. 함수 타입 정의하기

    ```jsx
    interface GetInfoText {
      (name: string, age: number): string;
    }
    const getInfoText: GetInfoText = function(name, age) {
     return '';
    }
    // 함수를 정의할 때에는 속성 이름 없이 정의한다. 
    ```

  c. 클래스 구현하기

    ```jsx
    interface Person {
      name: string;
      age: number;
      isYoungerThan(age: number): boolean;
    }

    Class SomePerson implements Person {
      name: string;
      age: number;
      constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
      }
      isYoungerThan(age: number) {
        return this.age < age;
      }
    }
    // implements 키워드를 사용해 클래스를 구현할 수 있다. 인터페이스에서 정의한 세 속성을 클래스 내부에서 구현했으며, 하나의 속성이라도 구현하지 않으면 컴파일 에러가 발생한다. 
    ```

  d. 인터페이스 확장하기

    ```jsx
    interface Person {
      name: string;
      age: number;
    }

    interface Programmer {
      favoriteLanguage: string;
    }

    interface Korean extends Person, Programmer {
      isLiveInSeoul: boolean;
    }

    const dabin: Korean = {
      name: 'dabin',
      age: 25,
      favoriteLanguage: 'javascript',
      isLiveInSeoul: true
    }

    // 인터페이스 합치기

    type KoreanProgrammer = Programmer & Korean;

    ```
    
# 타입 호환성
    
* 숫자와 문자열
    *  number나 string은 number | string 타입에 할당 가능하지만
    *  number | string 타입은 number나 string 타입에 할당이 불가능하다. 
* 인터페이스
    *  인터페이스 A가 인터페이스 B에 할당 가능하려면
        * B에 있는 모든 필수 속성의 이름이 A에도 존재해야 한다. 
        * 같은 속성 이름에 대해 A의 속성이 B의 속성에 할당 가능해야 한다. 

        ```jsx
        interface Person {
          name: string;
          age: number;
        }
        interface Product {
          name: string;
          age: number;
        }
        const person: Person = { name: 'mike', age: 23 };
        const product: Product = person;
        ```

    * 선택 속성이 타입 호환성에 미치는 영향
        * A를 B에 할당하려면 B 값의 집합이 A 값의 집합보다 커야 한다.

        ```jsx
        interface Person {
          name: string;
          age: number;
        }

        interface Product {
          name: string;
          age?: number;
        }
        // Person은 Product에 할당 가능하지만, Product는 Person에 할당이 불가능하다. 
        ```

    * 추가 속성과 유니온 타입이 타입 호환성에 미치는 영향

        ```jsx
        interface Person {
          name: string;
          age: number;
          gender :string;
        }

        interface Product {
          name: string;
          age?: number | string;
        }

        // 추가 속성이 있으면 값의 집합은 더 작아지므로 Person을 Product에 할당할 수 있다. 
        // Persond의 집합이 Product에 포함된다. 
        ```


    * 함수의 타입 호환성

      함수는 호출하는 시점에 문제가 없어야 할당 가능한다. 

      A를 B에 할당할 때 
        - A의 매개변수 개수가 B의 매개변수 개수보다 적어야 한다.
        - 같은 위치의 매개변수에 대해 B의 매개변수가 A의 매개변수로 할당 가능해야 한다.
        - A의 반환값은 B의 반환값으로 할당 가능해야 한다.

        ```jsx
        function addOne(value: number) {
          return value + 1;
        }

        const result = [1,2,3].map<number>(addOne);
        //(value: number, index: number, array: number[]) => number
        ```

# 타입스크립트 고급 기능

* 제네릭

타입 정보가 동적으로 결정되는 타입이다. 같은 규칙을 여러 타입에 적용할 수 있어 중복 코드를 제거할 수 있다는 장점이 있다. 

```jsx
function makeArray<T>(defaultValue: T, size: number): T[] {
  const arr: T[] = [];
  fot (let i = 0; i < size; i++) {
    arr.push(defaultValue);
  }
  return arr;
}

const arr1 = makeArray<number>(1,10);

// 사실.. 함수의 첫 번재 매개변수 타입을 알면 T를 알 수 있기 때문에 제네릭을 명시적으로 전달하지 않아도 된다. 
```
extends 키워드로 제네릭 타입을 제한할 수도 있다. 
```jsx
function identity<T extends number | string>(p1: T): T {
  return p1;
}

// 이 함수에는 숫자나 문자열 타입만 매개변수로 입력될 수 있다. 
```
keyof 키워드는 인터페이스의 모든 속성 이름을 유니온 타입으로 만들어 준다. 
```ㅓ
``jsx
function swapProperty<K extends keyof Person>{...}
```

* 맵드 타입

기존 인터페이스의 모든 속성을 선택 속성 또는 읽기 전용으로 만들 때 주로 사용된다. 

```jsx
type T1 = { [K in 'prop1' | 'prop2']: boolean };
// { prop1: boolean; prop2: boolean;}

type T1 = Person['name'] //string 
// 인터페이스에서 특정 속성의 타입을 추출할 때 사용되는 문법이다. 

type Readonly<T> = { readonly [P in keyof T]: T[P] };
type Partial<T> = { [P in keyof T]?: T[P] };
// 모든 속성을 선택 속성으로 만들어 준다. 
type P2 = Readonly<Person>
// Person의 모든 속성은 읽기 전용 속성으로 만든다. 
```
Pick 내장 타입은 원하는 속성만 추출할 때 사용된다. 
```jsx
type Pick<T, K extends keyof T> = { [P in K]: T[P] };
interface Person {
  name: string;
  age: number;
  language: string;
}
type T1 = Pick<Person, 'name' | 'language'>;
```
Record 내장 타입은 입력된 모든 속성을 같은 타입으로 만들어준다. 
```jsx
type Record<K extends string, T> = { [P in K]: T };
type T1 = Record<'p1' | 'p2', Person>;
//type T1 = { p1: Person; p2: Person; }
// K로 입력된 모든 문자열을 속성 이름으로 하면서 T를 각 속성의 타입으로 만든다. 
```
맵드 타입을 이용하면 열거형 타입의 활용도를 높일 수 있다. 
```jsx
enum Fruit {
  Apple, 
  Banana, 
} 

const FRUIT_PRICE: { [key in Fruit]: number }  = {
  [Fruit.Apple]: 10000
}

// 객체가 enum의 모든 원소를 속성으로 가지고 있어야 한다. 따라서 바나나가 없기 때문에 타입 에러가 난다. 
```

* 조건부 타입

조건부 타입은 입력된 제네릭 타입에 따라 타입을 결정할 수 있는 기능이다. 조건부 타입은 extends 키워드와 ? 기호를 사용해서 정의한다. 

```jsx
type IsStringType<T> = T extends string ? 'yes' : 'no';
type T1 = IsStringType<string>: //'yes'
```
Exclude는 서브타입을 제거해주고 Extract는 반대로 작동한다. 
```jsx
type Exclude<T, U> = T extends U ? never : T;
type T2<1 | 3 | 5 | 7, 1 | 5 | 9>; // 3 | 7

type Extract<T, U> = T extends U ? T : never;
type T3 = Extract<1 | 3 | 5 | 7, 1 | 5 | 9>; // 1 | 5
```
ReturnType 내장 타입은 함수의 반환 타입을 추출한다. 
```jsx
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
type T1 = ReturnType<() => string>; //string

// 타입 추론을 위해 infer 키워드가 사용되었으며, infer 키워드는 조건부 타입을 정의할 때 extends 키워드 뒤에 사용한다. 
```

# 생산성을 높이는 타입스크립트의 기능

* 타입추론

  * let 변수는 재할당 가능하기 때문에 융통성 있게 타입이 결정되며, 변수 선언시 값의 타입을 추론해 타입 에러를 발생시킨다. 

  * const 변수는 리터럴 자체가 타입이 된다. 즉, 123을 입력할 경우 number가 아닌 123이 타입이 된다. 

  * [1,2,3] 배열의 경우 number[]로 추론이 된다. 

  * 비구조화 할당에도 타입 추론이 되며 다른 타입을 비교하려고 하면 타입 에러가 난다. 

  * 함수의 매개변수와 반환값도 타입 추론이 적용된다. 

* 타입 가드

조건문을 이용해 타입의 범위를 좁히는 기능이다. 불필요한 타입 단언 코드를 피할 수 있으므로 생산성과 가독성이 높아진다. 

```jsx
// typeof를 통해 value를 숫자로 인식하기 때문에 숫자의 메서드를 호할 수 있다. 

function print(value: number | string) {
  if (typeof value === 'number') {
    console.log(value.toFixed(2));
  }
}


// instanceof: 클래스의 경우 이 키워드가 타입 가드로 사용될 수 있다. 


// 식별 가능한 유니온 타입

// 두 인터페이스에 type이라는 같은 이름의 속성을 정의하고 각 속성을 고유의 문자열 리터럴 타입으로 정의한다. 
// type 속서의 값을 비교하는 것으로 타입 가드가 동작한다. 
// switch 문에서 사용하기 좋다. 

interface Person {
  type: 'person';
  name: string;
  age: number;
}
interface Product {
  type: 'product';
  name: string;
  price: number;
}

function print(value: Person | Product) {
  switch (value.type) {
    case 'person':
      console.log(value.age);
      break;
    case 'product':
      console.log(value.price);
      break;
    }
}

// in키워드: 속성이 있는지 검사하면 속성 이름의 존재를 검사하는 것으로 타입 가드가 동작한다. 
function print(value: Person | Product) {
  if ( 'age' in value ) {
    console.log(value.age);
  } else {
    console.log(value.price);
  } 
}
```
    
# 타입스크립트 환경 구축하기

- tsconfig.json 파일에 “allowJs”: true 옵션을 추가하고 컴파일하면 타입스크립트와 자바스크립트를 함께 사용할 수 있다.
- 자바스크립트 최신문법을 사용하기 위해 폴리필을 추가해주어야 한다.
- 자바스크립트가 아닌 모듈(이미지 등)의 타입을 정의해준다.
    
# 리액트에 타입 적용하기
    
* 이벤트 처리 함수의 타입을 자주 작성하기 때문에 미리 타입을 만들어 놓으면 편하다. 

  ```jsx
  type EventObject<T = HTMLElement> = React.SyntheticEvent<T>;
  type EventFunc<T = HTMLElement> = (e: EventObject<T>) => void;
  ```

* 함수형 컴포넌트 타입 정의

  ```jsx
  interface Props {
    name: string;
    age?: number;
  }

  export default function MyComponent({name, age = 23}: Props) {
    return...
  }

  const MyComponent: React.FunctionComponent<Props> = function({name, age = 23}) {
    return...
  }
  ```
