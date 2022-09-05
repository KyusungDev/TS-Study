
## 목차
- [이펙티브 타입스크립트 2장 - 타입스크립트의 타입시스템 (아이템 16~18)](#이펙티브-타입스크립트-2장---타입스크립트의-타입시스템-아이템-1618)
  - [아이템 16) number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기](#아이템-16-number-인덱스-시그니처보다는-array-튜플-arraylike를-사용하기)
  - [아이템 17) 변경 관련된 오류 방지를 위해 readonly 사용하기](#아이템-17-변경-관련된-오류-방지를-위해-readonly-사용하기)
  - [아이템 18) 매핑된 타입을 사용하여 값을 동기화하기](#아이템-18-매핑된-타입을-사용하여-값을-동기화하기)
- [이펙티브 타입스크립트 3장 - 타입 추론 (아이템 19~20)](#이펙티브-타입스크립트-3장---타입-추론-아이템-1920)
  - [아이템 19) 추론 가능한 타입을 사용해 장황한 코드 방지하기](#아이템-19-추론-가능한-타입을-사용해-장황한-코드-방지하기)
  - [아이템 20) 다른 타입에는 다른 변수 사용하기](#아이템-20-다른-타입에는-다른-변수-사용하기)

# 이펙티브 타입스크립트 2장 - 타입스크립트의 타입시스템 (아이템 16~18)

## 아이템 16) number 인덱스 시그니처보다는 Array, 튜플, ArrayLike를 사용하기

✅ number 타입을 인덱스 시그니처로 사용하지 않아야 하는 이유

자바스크립트에서 객체란 키/값 쌍의 모음이고, 키는 보통 문자열이다. 숫자를 키로 사용하면 문자열로 변환된다.  
하지만, 숫자 인덱스를 사용하여 요소에 접근할 수 있다.

```ts
const x = { 1: 2, 3: 4 };
Object.keys(x); // ['1', '3']

const x = [1, 2, 3];
typeof x; // 'object' // 배열도 객체
object.keys(x); // ['0', '1', '2'] // 키값도 문자로 생성 됨

x[0]; // 1, 숫자로도 접근 가능
```

타입스크립트는 이러한 혼란을 바로잡기 위해 숫자 키를 허용하고, 문자열 키와 다른 것으로 인식한다.

```ts
interface Array<T> {
  [n: number]: T;
}
```

위의 경우 `number` 타입을 인덱스 시그니처로 사용하였지만, 일반적으로 `string` 대신 `number`를 타입의 인덱스 시그니처로 사용할 이유는 많지 않다.

- 숫자 속성이 어떤 특별한 의미를 지닌다는 오해를 불러 일으킬 수 있다.
- `number`를 인덱스 시그니처로 사용하기 보다 `Array`, `Tuple`, `ArrayLike` 타입을 사용하는 것이 좋다.

## 아이템 17) 변경 관련된 오류 방지를 위해 readonly 사용하기

✅ `readonly`를 사용하여 의도하지 않게 매개변수가 변경되는 것을 방지하기

매개변수로 객체를 전달할 경우, 해당 함수는 해당 객체의 값을 변경할 수 있으므로 의도하지 않은 문제가 발생할 수 있다. 이런 상황에서는 `readonly` 접근제어자를 사용하여 매개변수 변경을 방지할 수 있다.

```ts
function arraySum(arr: readonly number[]) {
  let sum = 0,
    num;
  while ((num = arr.pop()) !== undefined) {
    // ~~~'readonly number[]'형식에 'pop' 속성이 없습니다.
    sum += num;
  }
  return sum;
}
```

타입스크립트 컴파일러에 오류를 통해 `pop()` 호출을 인지하여 값 변경 없는 코드로 다시 작성할 수 있다.

```ts
function arraySum(arr: readonly number[]) {
  let sum = 0;
  for (const num of arr) {
    sum += num;
  }
  return sum;
}
```

`readonly`는 얕게 동작하므로 필요에 따라 `Readonly<>` 제네릭과 ts-essentials에 있는 DeepReadonly 제네릭 사용해야 한다.

```ts
// 얕게 동작하는 readonly
const dates: readonly Date[] = [new Date()];
dates.push(new Date()); // ~~~~ 'readonly Date[]' 형식에 'push' 속성이 없습니다.
dates[0].setFullYear(2037); // OK

// Readonly 제네릭 사용
interface Outer {
  inner: {
    x: number;
  };
}

const o: Readonly<Outer> = { inner: { x: 0 } };
o.inner = { x: 1 }; // ~~~~ 읽기 전용 속성이기 때문에 'inner'에 할당할 수 없습니다.
o.inner.x = 1; // OK

type T = Readonly<Outer>;
// Type T = {
//   readonly inner: {
//     x: number;
//   };
// }
```

인덱스 시그니처에 `readonly`를 사용할 경우 읽기만 허용하고 쓰기를 방지하여 객체 속성의 변경을 방지할 수 있다.

```ts
let obj: { readonly [k: string]: number } = {}; // Or Readonly<{[k: string]: number}
obj.hi = 45; //  ~~ ... 형식의 인덱스 시그니처는 읽기만 허용됩니다.
obj = { ...obj, hi: 12 }; // OK
obj = { ...obj, bye: 34 }; // OK
```

## 아이템 18) 매핑된 타입을 사용하여 값을 동기화하기

✅ 정확히 같은 속성을 가진 객체의 값을 비교할 때, 매핑된 타입을 사용하여 타입스크립트가 코드에 제약을 강제하도록 하는 방법

`REQUIRES_UPDATE`를 통해서 `ScatterProps`가 동일한 속성을 가져야 한다는 정보를 제공한다.

```ts
const REQUIRES_UPDATE: { [k in keyof ScatterProps]: boolean } = {
  xs: true,
  ys: true,
  xRange: true,
  yRange: true,
  color: true,
  onClick: false,
};

function shouldUpdate(oldProps: ScatterProps, newProps: ScatterProps) {
  let k: keyof ScatterProps;
  for (k in oldProps) {
    if (oldProps[k] !== newProps[k] && REQUIRES_UPDATE[k]) {
      return true;
    }
  }
  return false;
}
```

나중에 새로운 속성이 추가되는 경우에도 다음과 같은 오류가 발생하는 것을 알 수 있다.

```ts
interface ScatterProps {
  // ...
  onDoubleClick: () => void;
}

const REQUIRES_UPDATE: { [k in keyof ScatterProps]: boolean } = {
  // ~~~~~~~~~~~~~~~~~'onDoubleClick' 속성이 타입에 없습니다.
  // ...
};
```

# 이펙티브 타입스크립트 3장 - 타입 추론 (아이템 19~20)

타입스크립트는 타입 추론을 적극적으로 수행하고 수동으로 명시해야 하는 타입 구문 수를 엄청나게 줄여주기 때문에 코드의 전체적인 안정성이 향상된다. 타입스크립트 초보자와 숙련자는 타입 구문의 수에서 차이가 난다. 숙련된 타입스크립트 개발자는 비교적 적은 수의 구문을 사용하지만, 초보자의 코드는 불필요한 타입 구문으로 도배되어 있을 것이다.

## 아이템 19) 추론 가능한 타입을 사용해 장황한 코드 방지하기

✅ 타입 구문을 불필요하게 작성하지 않는 방법

모든 변수를 선언할 때 타입을 선언하는 것은 비생산적이며 형편없는 스타일이다.

```ts
// ❌  Don't do this
let x: number = 12;

const person: {
  name: string;
  born: {
    where: string;
    when: string;
  };
  died: {
    where: string;
    when: string;
  };
} = {
  name: 'Sojourner Truth',
  born: {
    where: 'Swartekill, NY',
    when: 'c.1797',
  },
  died: {
    where: 'Battle Creek, MI',
    when: 'Nov. 26, 1883',
  },
};
```

타입스크립트는 예상보다 더 정확하게 추론한다.

```ts
const axis1: string = 'a'; // 타입이 string
const axis2 = 'y'; // 타입이 "y", string이 아니라 'y'가 더 정확한 타입이다.
```

명시적으로 타입을 작성하는 것은 생산성 측면에서 비효율적이다. 하지만 매개변수의 product와 같이 타입스크립트가 스스로 판단하기 어려운 상황에서는 명시적 타입 구문이 필요하다.

```ts
interface Product {
  id: number; // 나중에 string으로 변경해야 하는 경우,
  name: string;
  price: number;
}

function logProduct(product: Product) {
  const id: number = product.id; // id 타입을 string으로 변경하면 에러가 발생 함
  const name: string = product.name;
  const price: number = product.price;
  console.log(id, name, price);
}

// 타입을 명시하지 않고 비구조화 할당을 통해 더 간결하게 작성하고 타입 추론도 가능하다.
function logProduct(product: Product) {
  const { id, name, price } = product;
  console.log(id, name, price);
}
```

> 이상적인 타입스크립트 코드는 함수/메서드 시그니처에 타입 구문을 포함하지만, 함수 내에서 생성된 지역 변수에는 타입 구문을 넣지 않는다. 타입 구문을 생략하여 방해되는 것들을 최소화하고 코드를 읽는 사람이 구현 로직에 집중할 수 있게 하는 것이 좋다.

타입이 추론될 수 있음에도 여전이 타입을 명시하고 싶은 몇 가지 상황이 있다.

1. 객체 리터럴을 정의하는 경우

- 잉여 속성 체크가 동작하여, 변수를 할당하는 시점에 선택적 속성이 있는 타입의 오타 등을 표시해준다.

```ts
interface Product {
  id: string;
  name: string;
  price: number;
}

// 객체 리터럴로 정의
const elmo: Product = {
  name: 'Tickle Me Elmo',
  id: '048188 627152',
  price: 28.99,
};
```

2. 함수의 반환 타입

- 구현상의 오류가 함수를 호출한 곳까지 영향을 미치지 않도록 하기 위해 타입을 명시하는게 좋다.

```ts
const cache: { [ticker: string]: number } = {};
function getQuote(ticker: string) {
  if (ticker in cache) {
    return cache[ticker];
  }
  return fetch(`https://quotes.example.com/?q=${ticker}`)
    .then((response) => response.json())
    .then((quote) => {
      cache[ticker] = quote;
      return quote;
    });
}

// getQuote는 항상 Promise를 반환하므로 이럴 경우 오류가 발생한다.
getQuote('MSFT').then(considerBuying);
// ~~~~ Property 'then' does not exist on type
//        'number | Promise<any>'
//      Property 'then' does not exist on type 'number'

// ReturnType을 명시하는 방법을 이용해, 구현 상의 로직이 호출 시점으로 이어지지 않도록 한다
function getQuote(ticker: string): Promise<number> {
  if (ticker in cache) {
    return cache[ticker];
    // ~~~~~~~~~~~~~ Type 'number' is not assignable to 'Promise<number>'
  }
  // ...
}
```

> `eslint` 규칙 중 `typescript/no-inferrable-types` 규칙을 이용하여 작성된 모든 타입 구문이 정말 필요한지 확인 할 수 있다.

## 아이템 20) 다른 타입에는 다른 변수 사용하기

✅ 타입이 다른 경우에는 변수를 분리하여 사용

자바스크립트에서는 한 변수를 다른 목적을 가지는 다른 타입으로 재사용해도 된다. 하지만 서로 연관 없는 변수는 분리하여 별도의 타입을 정의해서 사용하자.

```ts
let id = '12-34-56';
fetchProduct(id);

id = 123456; // ~~ '123456' is not assignable to type 'string'.
fetchProductBySerialNumber(id);

// ❌  Don't do this
// 유니온 타입으로 확장하여 사용
let id: string | number = '12-34-56';
id = 123456; // 정상
fetchProductBySerialNumber(id); // 정상

// ✅  Do this
// 변수를 분리하여 사용
const id = '12-34-56';
fetchProduct(id);

const serial = 123456;
fetchProductBySerialNumber(serial); // 정상
```

타입이 다른 경우 변수를 분리하는 이유

- 변수명을 구체적으로 지을수 있다.
- 타입 추론을 향상시키며, 타입 구문이 불필요해진다.
- 타입이 좀 더 간결해진다. (`string|number` 대신 `string`과 `number`를 사용)
- `let` 대신 `const`를 사용하여 코드가 간결해지고, 타입 체커가 타입을 추론하기에도 좋다.
