## Utility Types
타입스크립트는 타입 변환에 도움이 되는 유용한 유틸리티 타입을 제공한다.  
다음은 자주 사용되는 7가지 유틸리티 타입에 대해서 설명한다.  

### `Pick<Type, Keys>`
Type에서 문자열 리터럴 또는 문자열 리터럴의 합집합이 될 수 있는 Keys 속성 집합을 선택하여 새 타입을 만든다.   
Keys의 값은 Type의 키여야 한다.
```ts
type User = {
  name: string
  age: number
  address: string
  occupation: string
}

type BasicUser = Pick<User, "name" | "age">

// type BasicUser = {
//   name: string;
//   age: number;
// }
```
  
### `Omit<Type, Keys>`
`Pick`과 반대되는 타입으로 생략할 키 집합을 선택하여 특정 속성을 제거하고 나머지 속성으로 새 타입을 만든다.
```ts
type User = {
  name: string
  age: number
  address: string
  occupation: string
}

type BasicUser = Omit<User, "address" | "occupation">

// type BasicUser = {
//   name: string;
//   age: number;
// }
```

### `Partial<Type>`
모든 속성이 옵션으로 설정된 타입을 구성한다.
```ts
type User = {
  name: string
  age: number
  address: string
  occupation: string
}

type PartialUser = Partial<User>

// type PartialUser = {
//   name?: string;
//   age?: number;
//   address?: string;
//   occupation?: string;
// }
```
  
#### `Required<Type>`
`Partial`과 반대되는 타입으로 Type의 모든 속성이 필수로 설정된 타입을 생성한다.
```ts
type PartialUser = {
  name: string
  age: number
  address?: string
  occupation?: string
}

type User = Required<PartialUser>

// type User = {
//   name: string;
//   age: number;
//   address: string;
//   occupation: string;
// }
```
  
#### `ReadOnly<Type>`
모든 속성이 읽기 전용으로 설정된 타입을 생성한다. 변수나 속성에 새 값을 재할당하면 경고가 발생한다.
```ts
type User = {
  name: string
  age: number
  address: string
  occupation: string
}

type ReadOnlyUser = Readonly<User>

const user: ReadOnlyUser = {
  name: "Peter",
  age: 24,
  address: "Toronto",
  occupation: "software_engineer"
}

user.name = "Tom"
// Cannot assign to 'name' because it is a read-only property.
```
  
#### `Record<Keys, Type>`
Keys의 속성 키와 Type의 값을 사용하여 객체 유형의 타입을 생성한다.
```ts
type User = {
  name: string
  age: number
  address: string
  occupation: string
}

type Team = Record<"player1" | "player2", User>

// type Team = {
//   player1: User;
//   player2: User;
// }
```
  
#### `ReturnType<Type>`
Type의 반환 타입으로 구성된 타입을 생성한다.
```ts
import axios from 'axios'
type Response = ReturnType<typeof axios> // AxiosPromise<any>

function callAPI(): Response {
  return axios("url")
}
```


#### `NonNullable<Type>`
Type에서 `null`과 `undefined`를 제외한 타입을 구성한다.
```ts
type T0 = NonNullable<string | number | undefined>;  // string | number
type T1 = NonNullable<string[] | null | undefined>;  // string[]
```

#### `Nullable<Type>`
`NonNullable`의 반대로 `null`과 `undefined`를 포함한 타입을 만들어서 사용할 수 있다.
```ts
type Nullable<T> = T | undefined | null;
```
