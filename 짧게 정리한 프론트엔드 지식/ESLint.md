
#### 1. ESLint

- 자바스크립트는 컴파일 과정이 없는 **인터프리터 언어**이기 때문에, 런타임 에러가 발생할 확률이 높다. 
	- 따라서, **린트** 작업으로 사전 에러를 잡아주는 것이 중요하다.
	- 또한 해당작업은 원활한 코딩 컨벤션 방향을 잡는 데에 크게 도움을 준다.

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

- prettier와 ESLint 충돌방지용 패키지가 필요하다면, 아래 명령어를 입력하자
```null
npm i -D eslint-config-prettier eslint-plugin-prettier
```

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
	- 해당 파일은 ESLint 기능을 설정하는 파일이다.

- 아래 코드와 코드의 내용이 더 궁금하다면, 코드 출처 링크로 이동해 확인해보자.
	- `eslintrc.js` 코드 출처 : [@blackeichi님의 포스트](https://velog.io/@blackeichi/ESLint%EB%9E%80)
```null
{
 "env": {
   "browser": true,
   "es6": true,
   "node": true
 },
 "parser": "@typescript-eslint/parser",
 "plugins": ["@typescript-eslint", "import"],
 "extends": [
   "airbnb",
   "airbnb/hooks",
   "plugin:@typescript-eslint/recommended",
   "plugin:prettier/recommended",
   "plugin:import/errors",
   "plugin:import/warnings"
 ],
 "parserOptions": {
   "ecmaVersion": 2020,
   "sourceType": "module",
   "ecmaFeatures": {
     "jsx": true
   }
 },
 "rules": {
   "linebreak-style": 0,
   "import/no-dynamic-require": 0,
   "import/no-unresolved": 0,
   "import/prefer-default-export": 0,
   "global-require": 0,
   "import/no-extraneous-dependencies": 0,
   "jsx-quotes": ["error", "prefer-single"],
   "react/jsx-props-no-spreading": 0,
   "react/forbid-prop-types": 0,
   "react/jsx-filename-extension": [
     2,
     { "extensions": [".js", ".jsx", ".ts", ".tsx"] }
   ],
   "import/extensions": 0,
   "no-use-before-define": 0,
   "@typescript-eslint/no-empty-interface": 0,
   "@typescript-eslint/no-explicit-any": 0,
   "@typescript-eslint/no-var-requires": 0,
   "no-shadow": "off",
   "react/prop-types": 0,
   "no-empty-pattern": 0,
   "no-alert": 0,
   "react-hooks/exhaustive-deps": 0
 },
 "settings": {
   "import/parsers": {
     "@typescript-eslint/parser": [".ts", ".tsx"]
   },
   "import/resolver": {
     "typescript": "./tsconfig.json"
   }
 }
}
```

#### 4. 코딩 컨벤션

- **코딩 컨벤션**이란, 컴퓨터가 이해하는 것에 중점을 두지 않고, 사람의 이해를 돕기 위해 추천하는 코딩 스타일을 제시하는 가이드라인을 의미한다.
- 위에서 언급한 **ESLint** 또한, JavaScript언어의 정적 분석 툴의 코딩 컨벤션 중 하나로, 현재 나와있는 컨벤션 중 제일 유명하고 활용도가 높다.

> **장점**
- 코드의 가독성을 높인다.
- 버그가 줄어든다.
- 유지보수가 원활해진다.


#### 5. Next.js의 ESLint 설치와 `eslint-plugin-jsx-a11y` 사용 예시

- Next.js에서의 [ESLint](https://nextjs-ko.org/docs/app/building-your-application/configuring/eslint)는 Next.js 개발환경을 세팅할 때 각종 `dependencies`와 함께 자동으로 설치된다.
	- 정확히는 설치 과정에서 eslint 설치 여부에 대한 선택문에서 yes를 선택했을 때 설치된다.
	- 본인이 개발하고 있는 프로젝트의 경우, `package.json`에 아래 패키지들이 설치되어 관리되고 있었다.
```json
  // ...
  "devDependencies": {
    // ...
    "eslint": "8.57.0",
    "eslint-config-next": "14.2.3",
    // ...
  }
```

- Next.js는 [`eslint-plugin-next`](https://www.npmjs.com/package/@next/eslint-plugin-next)이라는 ESLint 플러그인을 제공하며, 기본 구성 내에 이미 번들되어 있어 Next.js 애플리케이션의 일반적인 문제들을 잡아낼 수 있다고 한다. 
	- Rule Set : https://nextjs-ko.org/docs/app/building-your-application/configuring/eslint#eslint-plugin

- 추가적으로, ESLint 설치여부에서 yes를 선택했다면, Next.js에서의 ESLint 설정파일인 `.eslintrc.json`파일이 생성되어 있을 것이다.
	- 설정 가능한 옵션은 ["next/core-web-vitals"](https://web.dev/articles/vitals?hl=ko), "next", "Cancel"" 등이 있다.
	
> `.eslintrc.json`
```json
{
  "extends": "next/core-web-vitals" // 엄격한 core-web-vitals 규칙 세트와 함께 Next.js의 기본 ESLint 구성을 포함한다. ESLint를 처음 설정하는 개발자에게 권장되는 구성
}
```

- Next.js는 ESLint 구성에 `eslint-plugin-jsx-a11y` 플러그인을 포함하여 접근성 문제를 조기 발견하는 데 도움을 준다.
	- 이 내용은 [Next.js 사용 가이드 문서](https://nextjs.org/learn/dashboard-app/improving-accessibility)에도 있는 내용이며, ESLint 관련 패키지 사용 예시를 위하여 인용해왔다.
	- 예를 들어, 이 플러그인은 대체 텍스트가 없는 이미지, `aria-*` 및 `role` 속성이 잘못 사용된 경우 등을 감지하여 개발자에게 경고한다.

- 선택적으로, 이 기능을 사용해 보려면 `package.json` 파일에 다음과 같이 `next lint`를 스크립트로 추가해볼 수 있다.
> `package.json`
```json
{
  // ...
  "scripts": {
    "build": "next build",
    "dev": "next dev",
    "start": "next start",
    "seed": "node -r dotenv/config ./scripts/seed.js",
    "lint": "next lint"
  },
  // ...
}
```

- 그 다음, 터미널에서 `pnpm lint`를 실행하자.
```bash
pnpm lint

> 만약, pnpm이 내부 또는 외부 명령, 실행할 수 있는 프로그램, 또는 배치 파일이 아니라고 뜨면 아래 명령어 시도
npm run lint
```

![[npm run lint.png]]
![[npm run lint(2).png]]
- 뭔가 설치가 된 것을 확인할 수 있다. 
	- 마지막 줄에 `ESLint has successfully been configured. Run next lint again to view warings and errors` 글자가 보이는가? 경고 및 오류를 보려면 저 명령어를 호출하라는 뜻이다.

- 이제 한번 `pnpm init`나, `npm run lint` 명령어를 실행해보자.
	- **여기서!!** 만약 오류가 뜬다면, 현재 eslint v9를 삭제하고, v8.57.0을 설치해보자. (npm 기준 명령어는 아래와 같다.)
		- `npm uninstall eslint`
		- `npm install -D eslint@8.57.0`


![[lint 설치 후 npm run lint 실행하기 (1).png]]
![[lint 설치 후 npm run lint 실행하기 (2).png]]
- 대부분 import해놓고 사용하지 않은 부분에서 에러가 발생했고, 수정을 완료하니 `No ESLint warings or errors`라고 출력된다. (앞으로 조심하자)
