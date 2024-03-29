> **진행순서**
> 1. 아이스 브레이킹
> 2. 학습내용 공유
> 3. 느낀점, 질문 답변, 학습 내용 논의
> 4. 중요한 부분, 실용적인 부분, 새롭게 알게된 부분, 깊은 이해가 필요한 부분
> 5. 실무 적용 사례
> 6. 회고 - 좋았던점, 개선할점


## Everyday Types
#### 타입스크립트의 사용 목적 [본문](https://www.typescriptlang.org/ko/docs/handbook/2/basic-types.html)
- 프로그래머들이 작성하는 가장 흔한 오류는 타입 오류입니다: 다른 종류의 값이 예상되는 곳에 특정한 값이 사용된 경우입니다. 이는 단순한 오타, 라이브러리 API를 이해하지 못한 것, 런타임 동작에 대한 잘못된 가정 또는 다른 오류 때문일 수 있습니다. TypeScript의 목표는 JavaScript 프로그램의 정적 타입 검사자 입니다. 즉, 코드가 실행되기 전에 실행하고(정적), 프로그램 타입이 정확한지 확인하는 도구(타입 검사)입니다.


#### 타입을 항상 명시할 필요는 없다 [본문](https://www.typescriptlang.org/ko/docs/handbook/2/basic-types.html)
- msg가 string 타입을 가진다는 사실을 TypeScript에게 알려주지 않았더라도 TypeScript는 이를 알아낼 수 있습니다. 이는 기본 기능이며, 타입 시스템이 알아서 올바른 타입을 어떻게든 잘 알아낼 수 있다면 타입 표기를 굳이 적지 않는 것이 가장 좋습니다.



#### 타입스크립트 컴파일러는 ES3를 기본옵션으로 컴파일된다 [본문](https://www.typescriptlang.org/ko/docs/handbook/2/basic-types.html#%EB%8B%A4%EC%9A%B4%EB%A0%88%EB%B2%A8%EB%A7%81)
- TypeScript는 ES3라는 아주 구버전의 ECMAScript를 타겟으로 동작하는 것이 기본 동작입니다. --target 플래그를 설정하면 보다 최근 버전을 타겟으로 변환을 수행할 수도 있습니다. --target es2015를 실행하면 TypeScript가 ECMAScript 2015를 타겟으로 동작할 수 있으며, 이는 ECMAScript 2015가 지원되는 런타임이기만 하면 해당 코드가 실행될 수 있도록 변환된다는 의미입니다. 따라서 tsc --target es2015 input.ts를 실행하면 아래와 같은 출력을 얻게 됩니다.


#### Any를 사용을 권장하지 않는 이유 [본문](https://www.typescriptlang.org/ko/docs/handbook/2/basic-types.html#%EC%97%84%EA%B2%A9%EB%8F%84)
- 몇몇 경우에서 TypeScript는 값의 타입을 추론하지 않고 가장 관대한 타입인 any로 간주한다는 사실을 기억하시기 바랍니다. 이는 최악의 경우는 아닙니다. 어쨌든, 타입을 any로 간주하는 것은 일반적인 JavaScript에서는 당연한 일이기도 합니다.  
하지만, any를 사용하면 애초에 TypeScript를 사용하는 이유가 무색해지는 경우가 많습니다. 프로그램에서 타입을 더 구체적으로 사용할수록, 더 많은 유효성 검사와 도구 기능을 사용할 수 있으며, 이는 곧 코드 상에서 보다 적은 버그를 만나게 된다는 의미입니다. noImplicitAny 플래그를 활성화하면 타입이 any로 암묵적으로 추론되는 변수에 대하여 오류를 발생시킵니다.

#### 타입과 인터페이스 [본문](https://www.typescriptlang.org/ko/docs/handbook/2/everyday-types.html#%ED%83%80%EC%9E%85-%EB%8B%A8%EC%96%B8)
- 타입 별칭과 인터페이스의 차이점 타입 별칭과 인터페이스는 매우 유사하며, 대부분의 경우 둘 중 하나를 자유롭게 선택하여 사용할 수 있습니다. interface가 가지는 대부분의 기능은 type에서도 동일하게 사용 가능합니다. 이 둘의 가장 핵심적인 차이는, 타입은 새 프로퍼티를 추가하도록 개방될 수 없는 반면, 인터페이스의 경우 항상 확장될 수 있다는 점입니다.

#### null과 undefined [본문](https://www.typescriptlang.org/ko/docs/handbook/2/everyday-types.html#null%EA%B3%BC-undefined)
- JavaScript에는 빈 값 또는 초기화되지 않은 값을 가리키는 두 가지 원시값이 존재합니다.


#### Type Assertion [본문](https://radlohead.gitbook.io/typescript-deep-dive/type-system/type-assertion)
- TypeScript에서는 시스템이 추론 및 분석한 타입 내용을 우리가 원하는 대로 얼마든지 바꿀 수 있습니다. 이때 "타입 표명(type assertion)"이라 불리는 메커니즘이 사용됩니다. TypeScript의 타입 표명은 프로그래머가 컴파일러에게 내가 너보다 타입에 더 잘 알고 있고, 나의 주장에 대해 의심하지 말라고 하는 것과 같습니다.
- 타입 표명을 "타입 캐스팅(type casting)"이라 부르지 않는 이유는 일반적으로 캐스팅이란 말은 실행 시간에 어떤 동작이 일어날 것임을 내포하기 때문입니다. 하지만 "타입 표명(type assertions)"은 순수하게 컴파일 시간 구성물이고 당신의 코드가 어떤 식으로 분석되길 원하는지 컴파일러에게 힌트를 제공하는 수단입니다.




### Q&A
---
Q) 함수 인수로의 this는 어떻게 동작하는가?  
A) this는 함수 내에 this의 타입을 알려주는 용도일 뿐 제거된다.

- [참고 예제](https://www.typescriptlang.org/play?#code/MYewdgzgLgBARgVylcMC8MAmJgILYCmYUAdAI4IEBOAngMoEA2BwKVAFAESLLicCUAbgBQPFGBIBDTJgCiANyJQAMgEtoRal2CNVwANacANDAAWksJmYAVEAHM7zIcOEAzBGFarU5yzfuOBOxQpuoAXDAAEtYAssqyzITE-DAA3sIwMCHqJDqSEBBq0CQoDszaugYEmAKCMAD09TCAvBuAwHvCAL5AA)
- [This in javascript and typescript](https://fettblog.eu/this-in-javascript-and-typescript/)


Q) 자바스크립트 class의 extends와 타입스크립트 implement 차이  
A) 자바스크립트의 class의 extends는 클래스의 확장을 위한 자바스크립트 문법이고, 타입스크립트의 implement는 인터페이스의 구조를 클래스가 올바르게 구현했는지 확인하는 용도일뿐 코드는 전혀 변경되지 않는다.  

- [참고 예제](https://playcode.io/924083/)
- [extends와 implements](https://www.howdy-mj.me/typescript/extends-and-implements/)
- [MDN extends](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/extends)
- [Class Heritage](https://www.typescriptlang.org/docs/handbook/2/classes.html#class-heritage)


Q) interface와 type의 사용 구분  
A) 타입 별칭과 인터페이스의 차이점 타입 별칭과 인터페이스는 매우 유사하며, 대부분의 경우 둘 중 하나를 자유롭게 선택하여 사용할 수 있다. interface가 가지는 대부분의 기능은 type에서도 동일하게 사용 가능하다. 이 둘의 가장 핵심적인 차이는, 타입은 새 프로퍼티를 추가하도록 개방될 수 없는 반면, 인터페이스의 경우 항상 확장될 수 있다.

- [Handbook Interface](https://www.typescriptlang.org/ko/docs/handbook/2/everyday-types.html#%ED%83%80%EC%9E%85-%EB%8B%A8%EC%96%B8)


Q) Enum을 사용하는 이유, 사용하지 않을 이유  
A) Enum을 사용방법을 이해하고 상황에 따라 사용하면 된다. (동작을 정확히 이해하는 것은 필요하지만 사용 방식에 대한 논쟁은 성능적으로도 문법적으로도 별 차이가 없기때문에 큰 의미가 없다)
- [Handbook Object vs Enums](https://www.typescriptlang.org/docs/handbook/enums.html#objects-vs-enums)
- [Handbook Enums](https://www.typescriptlang.org/ko/docs/handbook/2/everyday-types.html#%EC%97%B4%EA%B1%B0%ED%98%95)
- [Stackoverflow enum vs as const](https://stackoverflow.com/questions/66862421/enum-vs-as-const)

> ```as const```를 사용하는 경우 타입을 재사용할 수 없다. 
> [참고 예제](https://www.typescriptlang.org/play?#code/PTAEHUFMBsGMHsC2lQBd5oBYoCoE8AHSAZVgCcBLA1UABWgEM8BzM+AVwDsATAGiwoBnUENANQAd0gAjQRVSQAUCEmYKsTKGYUAbpGF4OY0BoadYKdJMoL+gzAzIoz3UNEiPOofEVKVqAHSKymAAmkYI7NCuqGqcANag8ABmIjQUXrFOKBJMggBcISGgoAC0oACCbvCwDKgU8JkY7p7ehCTkVDQS2E6gnPCxGcwmZqDSTgzxxWWVoASMFmgYkAAeRJTInN3ymj4d-jSCeNsMq-wuoPaOltigAKoASgAywhK7SbGQZIIz5VWCFzSeCrZagNYbChbHaxUDcCjJZLfSDbExIAgUdxkUBIursJzCFJtXydajBBCcQQ0MwAUVWDEQC0gADVHBQGNJ3KAALygABEAAkYNAMOB4GRonzFBSqf12Ih8nLENJvjzQABGADc0sagng7gCIuYAApafTGe5WZQOe4AJTBFQ4ZoeMheRDi5zA9g0L5uMzMdgMZiQfiwaDqRIc+B6ERePl0hlMwR8pLYvngByoADkwgAcpAJHygioAPJfMjvQQhrSQI6oRwKVzSPCgJzunTDLD6FAIRDQ4SXX0ScXREQGDjYhZMVgcHhBRQo+WgAAiFCcsHqjVAAG9FCV7gQ1ereHuV-AJJwTyVnpBkqgr6BHhRmJh74oAL7k3X6yCG+Am1d103TgAgPe1gkXRBQGeDJ4mEXcSmvWD1TVLMFCpdUswfJCEgAJlQ9DUFwrCPy-SkaBghJBHw3kEMQyj4nVRU0P0VBMOw6DYNw5jCOIk93zEYQZVQbVgncakGAYRUGMEAIGJQ3kZLk5DtXE8ZpGkaTYOo5SEgUziqNw3TGNEoA)



Q) 유니온의 사용   
A) TypeScript Union 유형은 유형이 여러 값/유형으로 구성될 수 있는 경우에 사용
- [Typescript union type a deeper look](https://daily-dev-tips.com/posts/typescript-union-type-a-deeper-look/)


Q) 타입 가드란  
A) [Typescript deep dive - 타입가드](https://radlohead.gitbook.io/typescript-deep-dive/type-system/typeguard)

Q) get,set의 사용법 getter와 setter   
A) 
- [get](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/get)
- [set](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/set)
- [Javascript getter and setter](https://www.programiz.com/javascript/getter-setter)
- [JavaScript - 접근자 프로퍼티 (getter, setter)](https://velog.io/@bigbrothershin/JavaScript-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-getter-setter)


Q) void, never 타입  
A) 
- void: 함수에서 반환값 없을 때 표기, 명시하지 않을 경우 기본 void으로 추론되므로 대부분 생략 함.
- never: 절대 발생할 수 없는 타입의 표기 [타입스크립트 Never 타입 완벽 가이드](https://ui.toast.com/weekly-pick/ko_20220323)


Q) Unknwon과 Any  
A) 

Q) 타입이론  
A) 프로그래밍 언어는 정확한 연산결과를 위해 타입에러 대해서 에러를 발생시키고 타입 안정성을 지켜야할 필요가 있다.
구현된 언어를 해석해서 유효한 연산인지 파악하기 위해 타입 정보를 유추해야 한다.

- [함수형 프로그래밍을 위한 람다대수](https://studyingeugene.github.io/functional-programming/introduction-to-lambda-calculus/)
- [힌들리-밀너 타입 추론 Hindley-Milner Type Inference](https://www.palindrom615.dev/hindley-milner-type-inference)



