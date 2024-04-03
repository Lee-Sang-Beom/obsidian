
#### 0. 시작하기 전에

- 초기 세팅은 크게 2가지로 분류할 수 있다.
	- gitLab 세팅
	- 프로젝트 세팅

#### 1. gitLab

- Repository를 만들면 이미 회사 내의 개발자들은 그룹으로 묶여있어, 자동으로 push 및 pull 등의 권한을 가지게 된다.

##### 1-1. Repository 만들기
1. [회사 내부 gitLab](http://gitlab.deps.kr/) 접속
2. 우측 상단 **New project** 버튼 클릭
3. **Create blank project** 클릭
4. **Project name**에 해당 프로젝트를 나타내는 이름 입력(ex. 일자리포털 프론트엔드)
5. **Project slug**에 해당 프로젝트의 영문 이름 입력(ex.gnwp-front)
6. Visibility Level: **Private**(해당 repository의 접속 권한. 우리끼리 사용할 것이니 Private로 함)
7. Initialize repository with a README **체크 해제**
8. **Create project**클릭

##### 1-2. Webhook 세팅
- 관리자(admin) 권한을 가지고 있는 사용자가 **webhook** 세팅 및 자동 배포를 위한 **Jenkins** 연동 작업을 진행해야 한다.

##### 1-3. 기타
- Repository의 **pipeline**을 비활성화 하기 위해 다음과 같은 과정을 수행한다.
	- `Settings → CI/CD → Auto DevOps → Default to Auto DevOps pipeline 체크 해제`


#### 2. 프로젝트 세팅

- 해당 문서에서는 Vercel의 Next.js 13 버전을 기준으로 정리한다.

- 프로젝트의 기본 세팅은 다음과 같이 분류할 수 있다.
	1. 프로젝트 생성
	2. git 설정
	3. 프로젝트 디렉토리 구조 생성
	4. 기본적인 라이브러리 설치 및 세팅

##### 2-1. 프로젝트 생성
- Next.js 프로젝트는 `npx create-next-app` 명령어를 사용하여 생성한다. (node.js 설치 필수)
	-  아래의 설정에 맞게 프로젝트를 만들면 된다.

```powershell
//@ 뒤에 붙는 것은 next의 버전. latest는 최신 버전을 의미한다
npx create-next-app@latest
// 프로젝트 이름 입력
? What is your project named? » (프로젝트 이름)
// TypeScript 사용 유무(Yes 누르면 됨)
? Would you like to use TypeScript? » No / Yes
// ESLint 사용 유무(Yes 누르면 됨)
? Would you like to use ESLint? » No / Yes
// Tailwind CSS 사용 유무
? Would you like to use Tailwind CSS? » No / Yes
// src 디렉토리 사용 유무(No 권장)
? Would you like to use `src/` directory? » No / Yes
// App 디렉토리 사용 유무(Yes 누르면 됨)
? Would you like to use App Router? (recommended) » No / Yes
// alias 기본 설정 유무(Yes 권장)
? Would you like to customize the default import alias? » No / Yes
// 기본 설정할 alias 작성(그냥 Enter 누르기)
? What import alias would you like configured? » @/*
```

##### 2-2. git 설정
- git 설정은 터미널에서 진행한다.
	-  프로젝트 root경로로 이동하여, 아래와 같이 git 초기 세팅을 진행하면 된다.

```powershell
// git 기본 세팅
git init
// gitlab repository와 연결
git remote add origin (git repository 주소)
// 만약 기본 branch가 master라면 다음 명령어를 통해 main으로 변경해주자.
git branch -m master main
```

- 다음으로, 파일 탐색기에서 프로젝트의 `.git` 내부 hooks 디렉토리에 다음 두 개의 파일을 추가해야 한다.
	- 해당 내용은 git repository으로 공유되지 않기 때문에, 개발을 진행하는 개발자 모두가 직접 작업해야한다.

- `pre-push`
```powershell
#!/bin/sh

cmd.exe /c ".git\\hooks\\pre-push.cmd"
```

- `pre-push.cmd`
```powershell
@echo off
npm run build
if %ERRORLEVEL% EQU 0 (
  git push
) else (
  echo Build failed, aborting git push.
)
```

- 마지막으로, `main` 및 `dev` branch에 push를 진행한다.
```powershell
git add -A
git commit -m "initial commit"
git push origin main
git checkout -b dev
git push origin dev
```

- 이후에는, `git issue` 등록 및 각자의 `branch`로 `checkout`하여 개발을 진행하면 된다.


#### 3. 프로젝트 내 디렉토리 구조 생성

app 디렉토리에 대한 구조 생성은 다음을 참고하자.

[https://nextjs.org/docs/app/building-your-application/routing](https://nextjs.org/docs/app/building-your-application/routing)

프로젝트에서 추가로 만들어 주어야 하는 디렉토리는 다음과 같다.

- components: 공통 컴포넌트 모음 디렉토리
- fonts: 적용할 폰트 모음 디렉토리
- hooks: 커스텀 훅 디렉토리
- lib: 라이브러리 관련 디렉토리
- styles: global style 관련 디렉토리
- types: Interface, type 정의 파일 모음 디렉토리
- utils: 기타 추가 기능 파일 모음 디렉토리

또한, 추가로 만들어야 하는 파일은 다음과 같다.

- .env.development: 개발 모드시(npm run dev) 적용되는 .env
- [deploy-build.sh](http://deploy-build.sh), Dockerfile: 무중단 배포에 사용되는 파일
- middleware.ts(필요시): 모든 요청에 대해 공통으로 적용하는 로직을 구현한 파일

해당 디렉토리 및 파일들을 필요한 기능에 맞춰 구현하면 된다.

대표적으로 생성해야할 파일의 목록은 다음과 같다.

- app > api > […slug] > route.ts
    - Next.js 내부 라우팅 파일1
    - 프론트엔드는 api 요청시 내부 라우팅을 거쳐 백엔드로 요청을 던진다.
    - 해당 파일은 GET, POST 요청에 대해 사용자의 jwt 토큰 값을 헤더에 담아 던지는 로직이 구현되어 있다.
- components > 공통 컴포넌트 (Input, Dialog, Select 등등)
- pages > api > file > […slug].ts
    - Next.js 내부 라우팅 파일2
    - POST에 formData를 통해 파일을 담아 요청 보낼때 작동하는 내부 라우팅 파일
- styles > reset.css
    - 웹 요소의 스타일 초기화를 담당하는 css 파일
- utils > serverSideFetch.ts
    - next-auth를 사용하게 되면 서버사이드 컴포넌트와 클라이언트사이드 컴포넌트에서의 유저 세션 정보를 가져오는 방식이 달라진다.
    - 또한 서버사이드 컴포넌트에서 내부 라우팅으로 거칠때, 세션 정보에 접근 할 수 없어, 토큰 정보가 올바르게 담기지 않는 현상이 존재한다.
    - 따라서, 해당 함수는 서버사이드에서 api 요청시 세션에 저장되어있는 토큰값을 올바르게 담아 요청 할 수 있도록 구현되어 있다.
- utils > utils.ts
    - 프로젝트를 진행하는데에 있어서 필요한 util 함수 모음 파일

### 기본적인 라이브러리 설치 및 세팅

프로젝트를 진행하는데 필요한 라이브러리 설치를 진행해야 한다.

기본적으로 react, next 등은 설치가 되어있겠지만,

거의 필수로 사용하는 몇개의 라이브러리는 기본적으로 설치 하면 좋다

ex) axios, lodash, moment, prettier, react-icons