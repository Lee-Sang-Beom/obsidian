
#### 1. ESLint

- 자바스크립트는 컴파일 과정이 없는 **인터프리터 언어**이기 때문에, 런타임 에러가 발생할 확률이 높다. 
	- 따라서, **린트** 작업으로 사전 에러를 잡아주는 것이 중요하다.

> **Lint**? **Linter**?
- **Lint** : 소스 코드에 문제가 있는지 탐색하는 작업
- **Linter** : 린트 작업을 도와주는 소프트웨어 도구

- **ESLint**는 **JavaScript 및 TypeScript 코드에서 문제를 자동으로 찾아주는 도구**로, 주로 코드 품질을 높이고 버그를 방지하기 위해 사용된다.
	- **ESLint**는 코드 스타일 및 잠재적인 오류를 검사하며, 특정 규칙을 기반으로 코드가 일관되게 작성되도록 보장한다.
	- 간단히 말해서 에러가 있는 코드에 표시를 달아주는 도구라고 생각하면 된다.


#### 2. 주요기능

- **코드 품질 향상**: 코드를 분석해 잠재적인 오류와 성능 문제를 감지한다.
- **일관된 코드 스타일 유지**: 팀에서 정의한 규칙을 강제하여 일관된 코드 스타일을 유지한다.
	- 예를 들어, 들여쓰기, 따옴표 사용 방식, 세미콜론 여부 등을 검사할 수 있다.

- **커스터마이징 가능**: 미리 정의된 규칙을 사용할 수 있을 뿐 아니라, 프로젝트에 맞게 직접 규칙을 설정할 수 있다.
- **플러그인 지원**: React, TypeScript, Vue 등 다양한 라이브러리나 프레임워크와 통합하여 확장된 기능을 사용할 수 있다.


#### 3. 사용하기

- 먼저, 패키지를 아래 명령어를 입력하여 설치할 수 있다.
```shell
npm install eslint --save -dev
```

- 그다음, 설정 파일을 초기 세팅해준다.
```shell
npm eslint --init
```

- pre

- 설치 과정에서 몇가지 선택지가 나오면 상황에 알맞게 설정을 진행하면 된다.
```null
-  How would you like to use ESLint?
    1. To check syntax only
    2. To check syntax and find problems
    3. To check syntax, find problems, and enforce code style

- What type of modules does your project use?
    1. JavaScript modules (import/export)
    2. CommonJs (require/exports)
    3. None of these

- Which framework does your project use?
    1. React 
    2. Vue.js
    3. None of these

- Does your project use TypeScript? 
    1. No
    2. Yes

- Where does your code run? 
    1. Browser
    2. Node

-  What format do you want your config file to be in?
    1. JavaScript
    2. YAML
    3. JSON

-  Would you like to install them now? 
    1. no
    2. yes

- Which package manager do you want to use?
    1. npm
    2. yarn
    3. pnpm
```

- 선택을 완료하면, `.eslintrc.js`파일을 확인할 수 있을 것이다.

#### 4. 코딩 컨벤션

- **코딩 컨벤션**이란, 컴퓨터가 이해하는 것에 중점을 두지 않고, 사람의 이해를 돕기 위해 추천하는 코딩 스타일을 제시하는 가이드라인을 의미한다.
- 위에서 언급한 **ESLint** 또한, JavaScript언어의 정적 분석 툴의 코딩 컨벤션 중 하나로, 현재 나와있는 컨벤션 중 제일 유명하고 활용도가 높다.

> **장점**
- 코드의 가독성을 높인다.
- 버그가 줄어든다.
- 유지보수가 원활해진다.

