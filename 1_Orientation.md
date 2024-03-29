## Orientation

### 타입스크립트의 역사
타입스크립트는 C# 핵심 개발자인 Anders Hejlsberg 리드 하에 2년간 내부 개발을 거쳐 2012년 10월 1일에 공개되었다. ([GOTO 2012](https://www.youtube.com/watch?v=3dqZW_DqHIQ&ab_channel=GOTOConferences))
자바스크립트는 간단한 이벤트 핸들링용으로 개발된 언어로 수만줄 수십만줄의 코드를 구조화하고 모듈화 해야하는 대규모 응용프로그램을 작성하기가 어렵다는 문제가 있었다. 
또한 완전히 동적으로 타입이 결정되므로 정적 정보를 참고하는 IDE 인텔리센스 도구들의 자동완성, 오류 검사등의 도움을 받기 어려운 문제가 있었다.
이러한 문제들의 해결을 위해 자바스크립트의 고유한 기능은 유지한 상태로 정적타입과 새로운 기능들을 추가하여 자바스크립트 슈퍼셋인 타입스크립트가 개발되었다.

---
### 타입스크트의 동작
브라우저에서 타입스크립트를 실행하기 위해서는 자바스크립트 코드로 변환이 필요한데 이 과정을 컴파일이라고 한다.
타입스크립트 코드를 컴파일하면 타입스크립트 컴파일러에 의해 모든 타입과 타입스크립트 구문이 제거되고 완전한 자바스크립트 코드를 생성해낸다.
타입스크립트는 현재 4.7.4 버전이 릴리즈 되었으며, [로드맵](https://github.com/Microsoft/TypeScript/wiki/Roadmap)을 통해서 앞으로의 개발 방향을 알 수 있다.
![image](https://user-images.githubusercontent.com/6733004/176998645-88978d10-221b-44d0-b6da-7f7b50554cba.png)


---
### 정적타입과 동적타입
타입스크립트로 작업할 경우, 거의 모든 상황에서 타입스크립트는 자바스크립트로 컴파일(또는 트랜스파일)되며 실제로 런타임에서는 자바스크립트가 실행된다. 
코드는 정적타입으로 작성하지만 실행시에는 자바스크립트로 변환됐기 때문에 동적타입 언어로 동작한다.

#### 정적타입 언어
- 정적타입 시스템은 프로그램이 실행되기 전에 많은 오류를 잡아낼 수 있다.
- 소스 코드에 대한 정적타입 분석이 가능하다면 코드 실행 없이도 상황에 맞는 자동 완성 구문을 제공 받을 수 있다.
- 타입 작성에 대한 번거로움과 코드량 증가

자바스크립트와 같은 동적 타입 언어의 경우 배포 이후에도 버그가 발견되는 경우가 많다. 실제로도 자바스크립트 코드에 정적 타입 시스템이 있었다면 15% 정도의 버그가 커밋 전에 잡혔을것이라는 연구 결과 논문이 있다. ([Quantifying Detectable Bugs in JavaScript](https://www.microsoft.com/en-us/research/wp-content/uploads/2017/09/gao2017javascript.pdf))

#### 동적타입 언어
- 동적 타입 언어는 런타임에(프로그램 실행시) 자료형이 결정된다.
- 타입에 대한 제약이 약하기 때문에 코드 작성이 유연하고 쉽게 배울수 있다.
- 프로그램 실행시에 타입이 결정되기 때문에 실행중에 에러가 발생한다.

---
### 타입스크립트를 사용하는 이유?
- 에러의 사전 방지 
  - 정적타입 검사로  코드작성시에 오류를 발견하여 코드 품질을 높일 수 있다.
- 코드 자동 완성과 가이드
  - IDE의 도움을 받아 자동완성 기능을 제공받을 수 있다.
- 최신 문법 사용
  - ECMAScript 최신 표준과 유용한 프로포절들을 지원한다.
- 협업시 유용함 (개인적으로 느낀점)
  - 타입을 명시하고 제한하여 서로 커뮤니케이션의 비용을 줄일 수 있다. (함수 시그니처나 컴포넌트 파라메터를 가이드 받아 사용법 쉽게 유추 할 수 있다)
  - 타입스크립트만의 고유한 기능을 사용하여 더욱 명시적으로 타입을 제한할 수 있다. (enum, type, interface, Generic..)
  - 별도의 문서 없이도 API 요청과 응답 형태를 파악하고 사용법을 쉽게 유추하고 타입 검사를 할 수 있다. ([Open API Generator](https://egg-programmer.tistory.com/287))

### 타입스크립트 사용 실태
- [2021 State Of JS - 자바스크립트로 컴파일되는 언어](https://2021.stateofjs.com/ko-KR/other-tools/#javascript_flavors0)
![image](https://user-images.githubusercontent.com/6733004/176999425-7b708bf1-90f6-4db2-99dd-cd172c254206.png)

---
### 타입스크립트를 어떻게 시작해야 할까?
타입스크립트는 자바스크립트와는 다른 정적 언어다. 분명히 러닝커브가 존재하고 깊이있게 이해해야 한다.   
하지만 타입스크립트의 모든 기능을 알필요도 없고 전부를 사용하지도 않는다.  
**그러니, 타입체크와 자동완성을 도와주는 플러그인 정도로 생각하고 가볍게 시작해보자.**

---
### 참고
- [타입스크립트 핸드북](https://joshua1988.github.io/ts/why-ts.html#%EC%99%9C-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC-%EC%8D%A8%EC%95%BC%ED%95%A0%EA%B9%8C%EC%9A%94)
- [나무위키 타입스크립트](https://namu.wiki/w/TypeScript)
- [우아한 테크코스 - 타입스크립트](https://www.youtube.com/watch?v=ViS8DLd6o-E&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9CTech)
- [Typescript 어떻게 공부해야 하나요?](https://yozm.wishket.com/magazine/detail/1376/)
- [자바스크립트 개발자를 위한 타입스크립트](https://ahnheejong.gitbook.io/ts-for-jsdev/01-introducing-typescript/why-typescript)
- [TypeScript Desgin Goals](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals)
