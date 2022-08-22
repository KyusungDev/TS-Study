# 이펙티브 타입스크립트 1장 - 타입스크립트 알아보기

이펙티브 시리즈는 특정 주제에 대한 '두 번째 표준 책'을 지향하고 있습니다.  
이펙티브 타입스크립트는 타입스크립트를 효율적이고 효과적으로 작성하는 방법을 알려주는 책입니다.

> "이 책의 목표는 독자에게 타입스크립트나 자바스크립트를 가르치는 것이 아니라, 초가나 중급자가 전문가로 발전할 수 있게 돕는것 입니다."

## 목차
- [이펙티브 타입스크립트 1장 - 타입스크립트 알아보기](#이펙티브-타입스크립트-1장---타입스크립트-알아보기)
  - [책의 구조](#책의-구조)
  - [아이템 1) 타입스크립트와 자바스크립트 관계 이해하기](#아이템-1-타입스크립트와-자바스크립트-관계-이해하기)
  - [아이템 2) 타입스크립트 설정 이해하기](#아이템-2-타입스크립트-설정-이해하기)
  - [아이템 3) 코드 생성과 타입이 관계없음을 이해하기](#아이템-3-코드-생성과-타입이-관계없음을-이해하기)
  - [아이템 4) 구조적 타이핑에 익숙해지기](#아이템4-구조적-타이핑에-익숙해지기)
  - [아이템 5) any 타입 지양하기](#아이템-5-any-타입-지양하기)


## 책의 구조

아이템의 모음 (총 62개)

- 아이템: 타입스크립트의 어떤 측면에 대한 구체적인 조언을 담은 기술적인 에세이
  - 제목: 내용의 핵심
  - 본문: 필요한 이유, 구체적인 예제와 기술적인 설명
  - 요약: 아이템이 말하고자하는 바, 더 읽어야 하는지 여부

## 아이템 1) 타입스크립트와 자바스크립트 관계 이해하기

타입스크립트와 자바스크립트의 관계에 대해서 설명합니다.

- 타입스크립트는 자바스크립트의 상위집합입니다.
- 모든 자바스크립트는 타입스크립트이지만, 모든 타입스크립트가 자바스크립트는 아닙니다.
- 타입 시스템의 목표 중 하나는 런타임에 오류를 발생시킬 코드를 미리 찾아내는 것입니다.
- 타입 체커를 통과한 자바스크립트 프로그램을 타입스크립트 프로그램이라고 할 수 있다.
![image](https://user-images.githubusercontent.com/6733004/185899318-993ba603-ecdc-40e4-94ba-a99d8f2b76cd.png)

## 아이템 2) 타입스크립트 설정 이해하기

타입스크립트 noImplicitAny, strictNullChecks 설정에 대해서 설명합니다.

### noImplicitAny

noImplicitAny는 변수들이 미리 정의된 타입을 가져야 하는지 여부를 제어합니다.

```ts
function add(a, b) {
  // 매개변수에는 암시적으로 'any' 형식이 포함됩니다.
  return a + b;
}
```

> 새 프로젝트를 시작한다면 처음부터 noImplicitAny를 설정하여 코드를 작성할 때마다 타입을 명시하도록 해야 합니다. 그러면 타입스크립트가 문제를 발견하기 수월해지고, 코드의 가독성이 좋아지면, 개발자의 생산성이 향상됩니다. noImplicitAny 설정 해제는 자바스크립트로 되어 있는 기존 프로젝트를 타입스크립트로 전환하는 상황에만 필요합니다.

### strictNullChecks
strictNullChecks는 null과 undefined가 모든 타입에서 허용되는지 확인하는 설정입니다.

```ts
const x: number = null; // 에러 [Type 'null' is not assignable to type 'number']

const el = document.getElementById('status');
el.textContent = 'Ready'; // 에러 [Object is possibly 'null']

if (el) {
  el.textContent = 'Ready'; // null 체크 로직 추가
}
// 또는
el!.textContent = 'React'; // el이 null이 아님을 단언하는 (!)표기 추가
```

> strictNullChecks는 null과 undefined 관련된 오류를 잡아내는 데 많은 도움이 되지만 코드 작성을 어렵게 합니다. 새 프로젝트를 시작한다면 가급적 strictNullChecks를 설정하는 것이 좋지만, 타입스크립트가 처음이거나 자바스크립트 코드를 마이그레이션 하는 중이라면 설정하지 않아도 괜찮습니다.

프로젝트가 거대해질수록 설정 변경은 어려워질 것이므로, 가능한 한 초반에 설정하는게 좋습니다.
모든 체크를 설정하고 싶다면 strict 설정을 하면 됩니다. (엄격한 체크)

## 아이템 3) 코드 생성과 타입이 관계없음을 이해하기
큰 그림에서 보면 타입스크립트 컴파일러는 두 가지 역할을 수행 합니다. 이 두 가지는 완전히 독립적입니다.

- 최신타입스크립트/자바스크립트를 브라우저에서 동작할 수 있도록 구버전의 자바스크립트로 트랜스파일
- 코드의 타입 오류를 체크

### 타입 오류가 있는 코드도 컴파일이 가능합니다.
타입 오류가 있어도 타입 체크 기능과 트랜스파일 기능은 독립적으로 동작하기 때문에 컴파일이 가능합니다.

### 런타입에는 타입 체크가 불가능합니다.
instanceof 체크 구문은 런타임에 실행되지만 Rectangle은 타입이기 때문에 런타임에서는 아무런 역할을 할 수가 없습니다.
실제로 자바스크립트로 컴파일되는 과정에서 모든 인터페이스 타입 구문은 그냥 제거되어 버립니다.

```ts
interface Square {
  width: number;
}

interface Rectangle extends Square {
  height: number;
}

type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape instanceof Rectangle) {
    // ~~ 'Rectangle'은 형식만 참조하지만, 여기서는 값으로 사용되고 있습니다.
    return shape.width * shape.height; // 'Shape' 형식에 'height' 속성이 없습니다.
  } else {
    return shape.width * shape.width;
  }
}
```

런타임에 정보를 유지하는 방법은 실제 속성이 존재하는지 체크해 보는 것이다.

```ts
// 실제 타입이 존재하는지 체크
function calculateArea(shape: Shape) {
  if ('height' in shape) {
    shape; // 타입이 Rectangle
    return shape.width * shape.height;
  } else {
    shape; // 타입이 Square
    return shape.width * shape.width;
  }
}
```

런타임에 접근 가능한 타입 정보를 명시적으로 저장하는 '태그' 기법을 사용할 수도 있습니다.

```ts
interface Square {
  kind: 'square';
  width: number;
}
interface Rectangle {
  kind: 'rectangle';
  height: number;
  width: number;
}
type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape.kind === 'rectangle') {
    shape; // Type is Rectangle
    return shape.width * shape.height;
  } else {
    shape; // Type is Square
    return shape.width * shape.width;
  }
}
```

또 다른 방법으로는 타입과 값으로 모두 사용하기 위해 클래스로 만들어서 사용하는 방법도 있습니다.

```ts
class Square {
  constructor(public width: number) {}
}

class Rectangle extends Square {
  constructor(public width: number, public height: number) {
    super(width);
  }
}

type Shape = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape instanceof Rectangle) {
    shape; // 타입이 Rectangle
    return shape.width * shape.height;
  } else {
    shape; // 타입이 Square
    return shape.width * shape.width;
  }
}
```

### 타입 연산은 런타임에 영향을 주지 않습니다.

값을 정제하기 위해서는 런타임의 타입을 체크해야 하고 자바스크립트 연산을 통해서 변환을 수행해야 합니다.

```ts
function asNumber(val: number | string): number {
  return val as number;
}

function asNumber(val: number | string): number {
  return typeof val === 'string' ? Number(val) : val;
}
```

### 런타임 타입은 선언된 타입과 다를 수 있습니다.

네트워크 호출로부터 받아온 값으로 함수를 실행하는 경우에 선언된 타입과 다른 값이 전달될 수 있습니다.  
타입스크립트에서는 런타임 타입과 선언된 타입이 맞지 않을 수 있으니 타입이 달라지는 혼란스러운 상황을 가능한 피해야 합니다.

```ts
function setLightSwitch(value: boolean) {
  switch (value) {
    case true:
      //...
      break;
    case false:
      //...
      break;
    default:
      console.log('실행되지 않을까 봐 걱정됩니다.');
  }
}

interface LightApiResponse {
  lightSwitchValue: boolean;
}
async function setLight() {
  const response = await fetch('/light');
  const result: LightApiResponse = await response.json(); // 불리언이 아닐 수 있다!
  setLightSwitch(result.lightSwitchValue);
}
```

### 타입스크립트 타입으로는 함수를 오버로드할 수 없습니다.
타입스크립트는 타입과 런타임의 동작이 무관하기 때문에 함수 오버로딩이 불가능합니다.
하나의 함수에 대해 여러 개의 선언문을 작성할 수 있지만 구현체(implementation)는 오직 하나입니다.

```ts
// 선언부
function add(a: number, b: number): number;
function add(a: string, b: string): string;

// 컴파일 후
function add(a, b) {
  // 단 한개의 구현체
  return a + b;
}
```

### 타입스크립트 타입은 런타임 성능에 영향을 주지 않습니다.
타입과 타입 연산자는 자바스크립트 변환 시점에 제거되기 때문에, 런타임의 성능에 아무런 영향을 주지 못합니다. 단, 런타임 오버헤드가 없는 대신 타입스크립트 컴파일러는 빌드타임 오버헤드가 있습니다. 하지만 컴파일러는 매우 빠르고 증분(incremental) 빌드 시 더욱 빠릅니다.

> 타입스크립트가 컴파일하여 생성하는 자바스크립트를 오래된 런타임 환경을 지원하기 위해 호환성을 높이고 성능 오버헤드를 감안할지, 호환성을 포기하고 성능 중심 네이티브 구현체를 선택할지 문제에 마주할 수 있습니다. 호환성을 높여 헬퍼 코드가 추가되면 오버헤드가 생길것입니다. 하지만 이것은 언어 레벨의 문제이고 타입과는 전혀 무관합니다.

## 아이템4) 구조적 타이핑에 익숙해지기

### 구조적 타이핑 (Structural typing)

Ocaml, Haskell, Typescript는 구조적 타입 시스템을 이용합니다. 이름이 다르더라도 구조가 같으므로 다음과 같은 코드도 동작합니다.

```js
class A {
  method(input: string): number {...}
}
class B {
  method(input: string): number {...}
}

let a: A = new B(); // 통과
```

### 명목적 타이핑 (Nominal typing)

C++, Java와 같은 언어는 명목적 타입 시스템을 이용합니다. 이름이 다를 경우 다른 타입이므로 다음과 같은 코드는 불가능합니다.

```java
class A {
  method(input: string) {...}
}
class B {
  method(input: string) {...}
}

A a = new B(); // 에러
```

### 덕 타이핑 (Duck typing)
덕 타이핑은 타입이 없는 동적 언어에서 객체가 어떤 타입에 속하는 것으로 판별하는 방식을 가리키는 용어입니다. 실제로 타입이 없는 언어에서 사용되므로 인터페이스만 동일하면 동작하는 타이핑을 일컫는 용어이기도 합니다. 따라서 자바스크립트는 타입이 없는 동적 언어이기 때문에 덕 타이핑 기반이고 타입스크립트는 정적언어이기 때문에 컴파일타임에 덕타이핑과 같은 방식으로 타입을 검사하여 컴파일 에러를 발생시킵니다.

> 덕 타이핑: 그 것이 오리인지 100% 확실하지는 않다. 하지만 오리처럼 생겼고, 오리처럼 걷고, 오리처럼 헤엄치며, 오리처럼 꽥꽥 소리를 낸다. 이 정도의 추론 단서라면 내가 보는 것이 오리라고 판단해도 전혀 무리는 없을 것이다.

```ts
// 덕 타이핑 예제 코드
interface Quackable {
  quack(): void;
}

class Duck implements Quackable {
  quack() {
    console.log('Quack');
  }
}

class Person {
  quack() {
    console.log('Quack');
  }
}

function inTheForest(quackable: Quackable): void {
  quackable.quack();
}

inTheForest(new Duck()); // OK
inTheForest(new Person()); // OK
```

타입스크립트는 컴파일이 필요한 정적 타입 기반 언어이기 때문에 구조적 타이핑 시스템을 사용합니다.
어떤 함수의 매개변수 값이 모두 제대로 주어진다면 그 값이 어떻게 만들어졌는지 신경쓰지 않고 사용합니다.
따라서 Vector2D 타입을 받는 normalize 함수호출시에 x, y를 가진 객체를 받으면 타입 에러가 발생하지 않습니다.

```ts
interface Vector2D {
  x: number;
  y: number;
}

// 벡터의 길이를 구하는 함수 (2D)
function calculateLength(v: Vector2D) {
  return Math.sqrt(v.x _ v.x + v.y _ v.y);
}

interface Vector3D {
  x: number;
  y: number;
  z: number;
}

// 3D 벡터의 길이를 1로 만드는 정규화 함수
function normalize(v: Vector3D) {
  const length = calculateLength(v);
    return {
      x: v.x / length,
      y: v.y / length,
      z: v.z / length,
  };
}

// 함수 normalize는 1보다 조금 더 긴 길이를 가진 결과를 출력
normalize({x: 3, y: 4, z: 5}) // {x: 0.6, y: 0.8, z: 1}
```

클래스와 관련된 할당 문에서도 동일한 방식으로 동작합니다.

```ts
class C {
  foo: string;
  constructor(foo: string) {
    this.foo = foo;
  }
}

const c = new C('instance of C');
const d: C = { foo: 'object literal' }; // 정상
```

함수에서 매개변수의 속성들이 매개변수 타입에 선언된 속성만 가지는 경우를 봉인된(sealed), 정확한(precise) 타입이라고 부르고 열린 타입(타입 확장에 열려있는)은 이와 반대입니다. 즉 타입에 선언된 속성 외의 다른 속성들을 가지더라도 두 타입은 서로 **호환**됩니다.

## 아이템 5) any 타입 지양하기

any 타입을 사용하면 안되는 이유를 설명합니다.

### any 타입에는 타입 안정성이 없습니다.
as any를 사용함으로써 number타입에 string 데이터를 할당할 수 있습니다.

```ts
let age: number;
age = '12'; // ~~~ Type '"12"' is not assignable to type 'number'
age = '12' as any; // OK
```

### any는 함수 시그니처를 무시해 버립니다.
any 타입을 사용하면 약속된 호출 입력과 출력을 무시할 수 있습니다.

```ts
function calculateAge(birthDate: Date): number {
  // ...
}

let birthDate: any = '1990-01-19';
calculateAge(birthDate); // OK
```

### any 타입에는 언어 서비스가 적용되지 않습니다.
어떤 심벌에 타입이 있다면 타입스크립트 언어 서비스는 자동완성 기능과 적절한 도움말을 제공합니다. 그러나 any 타입인 심벌을 사용하면 아무런 도움도 받지 못합니다.

### any 타입은 코드 리팩터링 때 버그를 감춥니다.
```ts
interface ComponentProps {
  onSelectItem: (item: any) => void;
}

function renderSelector(props: ComponentProps) {}

let selectedId: number = 0;

function handleSelectItem(item: any) {
  selectedId = item.id; // item을 any로 받으므로 id만 받아도 통과되지만 런타임에는 에러가 발생
}

renderSelector({ onSelectItem: handleSelectItem });

// onSelectItem에 id만을 전달하도록 ComponentProps의 시그니처를 변경합니다.
interface ComponentProps {
  onSelectItem: (id: number) => void;
}
```

타입 체크는 통과하지만 `handleSelectItem`은 `any` 매개변수를 받으므로 id를 전달받아도 문제가 없다고 나옵니다. 하지만 `id`를 전달받으면 런타임에는 오류가 발생할 것입니다.

### any는 타입 설계를 감춰버립니다.
애플리케이션 상태 같은 객체를 정의할 때 수많은 속성의 타입을 일일이 작성하는 것은 번거롭습니다. 하지만 any를 사용하면 상태 객체의 설계를 감춰버리기 때문에 사용하면 안된다. 깔끔하고 정확하고 명료한 코드 작성을 위해 제대로 된 타입 설계는 필수입니다.

### any는 타입시스템의 신뢰도를 떨어뜨립니다.
any 타입을 쓰지 않아야 런타임에 발견될 오류를 미리 잡을 수 있고 신뢰도를 높을 수 있습니다. any 타입으로 인해 타입 오류를 고쳐야하고 머리속에 실제 타입을 기억해야 하기 때문에 오히려 자바스크립트보다 일을 더 어렵게 만들수도 있습니다. 타입스크립트가 타입 정보를 기억할 수 있도록 타입을 구체적으로 명시해주어야 합니다.

### 요약
`any` 타입을 사용하면 타입 체커와 타입스크립트 언어 서비스를 무력화 시켜버립니다. `any` 타입은 진짜 문제점을 감추며, 개발 경험을 나쁘게 하고, 타입 시스템의 신뢰도를 떨어뜨립니다. 최대한 사용을 피하도록 합니다.

--- 
### 참고
- [TypeScript와 Duck Typing의 관계 쉽게 설명하기](https://soopdop.github.io/2020/12/09/duck-typing/)
