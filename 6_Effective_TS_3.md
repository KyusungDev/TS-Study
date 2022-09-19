- [이펙티브 타입스크립트 3장 - 타입 추론 (아이템 26~27)](#이펙티브-타입스크립트-3장---타입-추론-아이템-2627)
  - [아이템 26) 타입 추론에 문맥이 어떻게 사용되는지 이해하기](#아이템-26-타입-추론에-문맥이-어떻게-사용되는지-이해하기)
    - [문맥과 값을 분리할 때 문맥의 소실로 인해 오류가 발생하는 경우](#문맥과-값을-분리할-때-문맥의-소실로-인해-오류가-발생하는-경우)
      - [튜플 사용 시 주의점](#튜플-사용-시-주의점)
      - [객체 사용 시 주의점](#객체-사용-시-주의점)
      - [콜백 사용 시 주의점](#콜백-사용-시-주의점)
  - [아이템 27) 함수형 기법과 라이브러리로 타입 흐름 유지하기](#아이템-27-함수형-기법과-라이브러리로-타입-흐름-유지하기)
- [이펙티브 타입스크립트 4장 - 타입 설계 (아이템 28~30)](#이펙티브-타입스크립트-4장---타입-설계-아이템-2830)
  - [아이템 28) 유효한 상태만 표현하는 타입을 지향하기](#아이템-28-유효한-상태만-표현하는-타입을-지향하기)
  - [아이템 29) 사용할 때는 너그럽게, 생성할 때는 엄격하게](#아이템-29-사용할-때는-너그럽게-생성할-때는-엄격하게)
  - [아이템 30) 문서에 타입 정보를 쓰지 않기](#아이템-30-문서에-타입-정보를-쓰지-않기)

# 이펙티브 타입스크립트 3장 - 타입 추론 (아이템 26~27)

## 아이템 26) 타입 추론에 문맥이 어떻게 사용되는지 이해하기

타입스크립트는 타입을 추론할 때 단순히 값만 고려하지는 않는다. 값이 존재하는 곳의 문맥까지 살핀다. 문맥을 고려하다 보면 가끔 이상한 결과가 나온다.

```ts
type Language = 'JavaScript' | 'TypeScript' | 'Python';
function setLanguage(language: Language) {
  /* ... */
}

setLanguage('JavaScript'); // 정상

let language = 'JavaScript';
setLanguage(language); // ❌ 'string' 타입의 인수는 'language' 형식의 매개변수에 할당할 수 없습니다.
```

**값을 변수로 분리했을 때 타입스크립트는 할당 시점에 타입을 추론한다.** 위의 경우는 string으로 추론했고, Language 타입으로 할당이 불가능하므로 오류가 발생하였다.

이러한 문제를 해결하는 데에는 두 가지 방법이 있다. 첫 번째는 타입 선언에서 language의 가능한 값을 제한하는 것이다. 두 번째는 language를 상수로 만들어 주는 것이다.

```ts
// 1) Language 타입 추가
let language: Language = 'JavaScript';
setLanguage(language); // 정상

// 2) const 선언
const language = 'JavaScript';
setLanguage(language); // 정상
```

### 문맥과 값을 분리할 때 문맥의 소실로 인해 오류가 발생하는 경우

#### 튜플 사용 시 주의점

이동이 가능한 지도를 보여주는 프로그램을 작성한다고 가정한다.

```ts
function panTo(where: [number, number]) {
  /* ... */
}

panTo([10, 20]); // 정상

const loc = [10, 20];
panTo(loc); // ❌ 'number[]' 형식의 인수는 '[number, number]' 형식의 매개변수에 할당할 수 없다.
```

여기서도 문맥과 값을 분리하였다. 첫 번째는 [10, 20]이 튜플 타입 `[number, number]`에 할당 가능하다. 두 번째 경우는 타입스크립트가 loc의 타입을 `number[]`로 추론한다. 따라서 해당 튜플 타입은 여기에 할당할 수 없다.

`any`를 쓰지 않고 오류를 고치는 방법은 loc의 타입을 `[number, number]`로 선언하거나 '상수 문맥'을 사용하는 것이다. `const`는 단지 값이 가리키는 참조가 변하지 않는 얕은(shallow) 상수인 반면, as const는 그 값이 내부(deeply)까지 상수라는 사실을 타입스크립트에게 알려준다.

```ts
const loc: [number, number] = [10, 20];
panTo(loc); // 정상
function panTo(where: readonly [number, number]) { /* ... */ }

const loc: [10, 20] as const; // 상수 단언
panTo(loc); // 정상

/*
만약 panTo의 인자 타입을 수정하지 않았다면
'readonly [10, 20]' 형식은 'readonly'이며 변경 가능한 형식 '[number, number]'에 할당할 수 없다. 에러가 발생했을 것이다.
*/

```

#### 객체 사용 시 주의점

문맥에서 값을 분리하는 문제는 문자열 리터럴이나 튜플을 포함하는 큰 객체에서 상수를 뽑아낼 때도 발생한다.

```ts
type Language = 'Javascript' | 'Typescript';
interface GovernedLanguage {
  language: Language;
  organization: string;
}
function complain(language: GovernedLanguage) {
  /* ... */
}
complain({ language: 'Typescript', organization: 'Microsoft' }); // 정상
const ts = {
  language: 'Typescript',
  organization: 'Microsoft',
};
complain(ts); // ❌ 에러
```

ts 객체에서 language의 타입은 string으로 추론된다. 타입 선언을 추가하거나 상수 단언을 사용하여 해결할 수 있다.

#### 콜백 사용 시 주의점

콜백을 다른 함수로 전달할 때, 타입스크립트는 콜백의 매개변수 타입을 추론하기 위해 문맥을 사용한다. 콜백은 상수로 뽑아내면 문맥이 소실되고 `noImplicitAny` 오류가 발생하게 되는데, 이런 경우는 매개변수에 타입 구문을 추가해서 해결할 수 있다.

```ts
function callWithRandom(fn: (n1: number, n2: number) => void) {
  fn(Math.random(), Math.random());
}
callWithRandom((a, b) => {
  a; // type: number
  b; // type: number
  console.log(a + b);
});

const fn = (a, b) => {
  console.log(a + b); // ❌ 'a', 'b' 매개변수에는 암시적으로 'any' 형식이 포함됩니다.
};
callWithRandom(fn);

// 매개변수에 타입 구문 추가
const fn = (a: number, b: number) => {
  console.log(a + b);
};
callWithRandom(fn); // 정상
```

## 아이템 27) 함수형 기법과 라이브러리로 타입 흐름 유지하기

jQuery, UnderScore, Lodash, Ramda 같은 라이브러리들의 일부 기능(map, flatMap, filter, reduce)은 순수 자바스크립트로 구현되어 있다. 이들은 타입스크립트와 조합하면 더 빛을 발한다. 그 이유는 타입 정보가 그대로 유지되면서 타입 흐름(flow)이 계속 전달되도록 하기 때문이다.

자바스크립트에서는 프로젝트에 서드파티 라이브러리(ex. Lodash) 종속성을 추가할 때 신중해야 한다. 하지만 **같은 코드를 타입스크립트로 작성하면 서드파티 라이브러리를 사용하는 것이 무조건 유리하다.** 타입 정보를 참고하며 작업할 수 있기 때문에 시간을 단축할 수 있다.

데이터의 가공(mungling)이 정교해질수록 이러한 장점은 더욱 분명해진다. 예를 들어 모든 NBA 팀의 선수 명단을 가지고 있다고 가정한다.

```ts
interface BasketballPlayer {
  name: string;
  team: string;
  salary: string;
}
declare const rosters: { [team: string]: BasketballPlayer[] };
```

직접 루프를 사용해 단순(flat) 목록을 만들려면 `concat`을 사용하고 allPlayers에 타입 구문을 추가해 주어야 한다.

```ts
let allPlayers: BasketballPlayer[] = [];
for (const players of Object.values(rosters)) {
  allPlayers = allPlayers.concat(players); // 정상
}
```

그러나 더 나은 해법은 `Array.prototype.flat`을 사용하는 것이다.

```ts
const allPlayers = Object.values(rosters).flat(); // 정상 타입이 BasketballPlayer[]
```

여러가지 연산을 처리할 때 로대시나 언더스코어 같은 라이브러리를 사용하면 체인의 개념을 사용하기 때문에, 더 자연스러운 순서로 연산을 작성할 수 있다. 체인을 사용하면 연산의 등장과 순서가 동일하게 된다.

```js
// 일반 연산
_.c(_.b(_.a(v)));

// 체인 연산
_(v).a().b().c().value(); // 여기서 _(v)는 값을 래핑(wrap)하고, .value()는 언래핑(unwrap)한다.
```

내장된 함수형 기법들과 로대시 같은 라이브러리에 타입 정보가 잘 유지되는 것은 우연이 아니다. 함수 호출 시 전달된 매개변수 값을 건드리지 않고 매번 새로운 값을 반환함으로써, 새로운 타입으로 안전하게 반환할 수 있다.

# 이펙티브 타입스크립트 4장 - 타입 설계 (아이템 28~30)

4장에서는 타입 자체의 설계에 대해서 다룬다. 4장의 예제들은 모두 타입스크립트를 염두에 두고 작성했지만, 대부분 다른 언어에도 적용될 수 있는 아이디어다.

## 아이템 28) 유효한 상태만 표현하는 타입을 지향하기

효과적인적으로 타입을 설계하려면 유효한 상태만 표현할 수 있는 타입을 만들어 내는 것이 가장 중요하다.

애플리케이션에서 페이지를 선택하면, 페이지의 내용을 로드하고 화면에 표시하는 예제다.

```ts
// 페이지의 상태
interface State {
  pageText: string;
  isLoading: boolean;
  error?: string;
}

// 페이지를 그리는 함수
function renderPage(state: State) {
  if (state.error) {
    return 'Error';
  } else if (state.isLoading) {
    return 'Loading';
  }
  return 'page';
}

// 페이지를 전환하는 함수
async function changePage(state: State, newPage: string) {
  state.isLoading = true;
  try {
    const response = await fetch(newPage);
    if (!response.ok) {
      throw new Error();
    }
    const text = await response.text();
    state.isLoading = false;
    state.pageText = text;
  } catch (e) {
    state.error = '' + e;
  }
}
```

위 코드의 `renderPage`는 분기 조건이 명확히 분리되어 있지 않다. `isLoading`이 `true`이고 동시에 `error`값이 존재하면 로딩 중인지 오류가 발생한 건지 명확히 구분할 수 없다.  
`changePage`에도 문제점이 많다. 오류가 발생했을 때 `state.isLoading`을 `false`로 설정하는 로직이 없다.  
`state.error`를 초기화하지 않았기 때문에, 페이지 전환 중에 로딩 메시지 대신 과거의 오류 메시지를 보여 주게 된다.  
페이지 로딩 중에 사용자가 페이지를 바꿔 버리면 어떤 일이 벌어질지 예상하기 어렵다.  
즉, `State` 타입은 `isLoading`이 `true`이면서 `error` 값이 설정되는 무효한 상태를 허용하는 것이다.  
**무효한 상태가 존재하면 두 함수 모두 제대로 구현할 수 없다.**

다음은 애플리케이션의 상태를 좀 더 제대로 표현한 방법이다.

```ts
// 네트워크 요청 과정 각각의 상태를 명시적으로 모델링하는 태그된 유니온을 사용했다.
interface RequestPending {
  state: 'pending';
}
interface RequestError {
  state: 'error';
  error: string;
}
interface RequestSuccess {
  state: 'ok';
  pageText: string;
}
type RequestState = RequestPending | RequestError | RequestSuccess;

interface State {
  currentPage: string;
  requests: { [page: string]: RequestState };
}

function renderPage(state: State) {
  const { currentPage } = state;
  const requestState = state.requests[currentPage];
  switch (requestState.state) {
    case 'pending':
      return 'Loading';
    case 'error':
      return 'Error';
    case 'ok':
      return 'Page';
  }
}
async function changePage(state: State, newPage: string) {
  state.requests[newPage] = { state: 'pending' };
  state.currentPage = newPage;
  try {
    const response = await fetch(newPage);
    if (!response.ok) {
      throw new Error();
    }
    const pageText = await response.text();
    state.requests[newPage] = { state: 'ok', pageText };
  } catch (e) {
    state.requests[newPage] = { state: 'error', error: '' + e };
  }
}
```

타입 관련 코드가 길어졌지만 무효한 상태를 허용하진 않는다. `renderPage`와 `changePage` 함수의 모호함도 완전히 사라졌다. 현재 페이지가 무엇인지 명확하며, 모든 요청은 정확히 하나의 상태로 맞아 떨어진다.

> 유효한 상태와 무효한 상태를 둘 다 표현하는 타입은 혼란을 초래하기 쉽고 오류를 유발하게 됩니다. 유효한 상태만 표현하는 타입을 지향해야 합니다. 코드가 길어지거나 표현하기 어렵지만 결국은 시간을 절약하고 고통을 줄일 수 있습니다.

## 아이템 29) 사용할 때는 너그럽게, 생성할 때는 엄격하게

보통 매개변수 타입은 반환 타입에 비해 범위가 넓은 경향이 있다. 매개변수는 타입의 범위가 넓어도 되지만, 반환 타입은 더 구체적이어야 한다. 매개변수 타입의 범위가 넓으면 편리하지만, 반환 타입의 범위가 넓으면 불편하다.

```ts
interface CameraOptions {
  center?: LngLat;
  zoom?: number;
  bearing?: number;
  pitch?: number;
}

type LngLat =
  | {
      lng: number;
      lat: number;
    }
  | { lon: number; lat: number }
  | [number, number];
type LngLatBounds =
  | {
      norttheast: LngLat;
      southwest: LngLat;
    }
  | [LngLat, LngLat]
  | [number, number, number, number];

declare function setCamera(camera: CameraOptions): void;
declare function viewportForBounds(bounds: LngLatBounds): CameraOptions;

function handleCamera(bounds: LngLatBounds) {
  const camera = viewportForBounds(bounds);
  const {
    center: { lat, lng }, // 'LngLat | undefined' 형식에 속성이 없습니다.
    zoom,
  } = camera;
}
```

`center`의 타입이 `LngLat | undefined`로 추론되며, `zoom`의 타입이 `number | undefined`로 추론된다. 근본적인 문제는 `viewportForBounds`의 타입 선언이 사용될 때뿐만 아니라 만들어질 때에더 너무 자유롭다는 것이다.

```ts
interface Camera {
  center: LngLat;
  zoom: number;
  bearing: number;
  pitch: number;
}
interface CameraOptions extends Omit<Partial<Camera>, 'center'> {
  center?: LngLatLike;
}

interface LngLat {
  lng: number;
  lat: number;
}
type LngLatLike = LngLat | { lon: number; lat: number } | [number, number];
type LngLatBounds =
  | {
      norttheast: LngLatLike;
      southwest: LngLatLike;
    }
  | [LngLatLike, LngLatLike]
  | [number, number, number, number];

declare function setCamera(camera: CameraOptions): void;
declare function viewportForBounds(bounds: LngLatBounds): Camera;

function handleCamera(bounds: LngLatBounds) {
  const camera = viewportForBounds(bounds);
  setCamera(camera);
  const {
    center: { lat, lng }, // 정상, center는 LatLng로, zoom은 number로 잘 추론된다.
    zoom,
  } = camera;
}
```

유니온 타입의 요소별 분기 방법은, 기본 형식을 구분하는 것이다. `array`와 `array-like`의 구분을 위해 `LngLat`와 `LngLatLike`로 구분할 수 있다. 완전하게 정의된 `Camera`와 부분적으로 정의된 `CameraOptions`로 구분할 수 있다.

> 선택적 속성과 유니온 타입은 반환 타입보다 매개변수 타입에 더 일반적이다. 매개변수와 반환 타입의 재사용을 위해서 기본 형태(반환 타입)와 느슨한 형태(매개변수 타입)를 같이 두자.

## 아이템 30) 문서에 타입 정보를 쓰지 않기

코드와 주석의 정보가 맞지 않으면 잘못된 상태인 것이다. 주석과 변수명에 타입 정보를 적는 것은 피해야 한다. 타입 선언이 중복되는 것으로 끝나면 다행이고, 타입 정보 자체에 모순이 발생할 수도 있다.

변수명을 `ageNum`으로 하는 것보다는 `age`로 하고 타입을 `number`로 명시하는 게 좋다. 그러나 단위가 있는 숫자들처럼, 타입이 명확하지 않은 경우는 변수명에 단위 정보를 포함하는 것을 고려하자. (timeMs, temperatureC)
