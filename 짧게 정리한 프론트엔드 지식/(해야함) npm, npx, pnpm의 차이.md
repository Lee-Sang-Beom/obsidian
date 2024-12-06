
#### 1. npm

- NPM(Node Packaged Manager)
	- Node.js로 만들어진 모듈을 웹에서 받아서 설치하고 관리해주는 패키지 매니저 프로그램
	- 전 세계의 개발자들이 자바스크립트로 만든 다양한 패키지를 **npm 온라인 데이터베이스**에 올리고, 사용자는 그것을 npm과 같은 패키지 관리자를 통해 설치 및 삭제가 가능하게 한다.
		- npm의 주 역할은 **패키지 관리**와 **스크립트 실행**이다.

| npm +                                 | 내용                                                                                                                                                          | 기타                                                                                                                                                                                                 |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| npm init                              | package.json 생성                                                                                                                                             |                                                                                                                                                                                                    |
| npm help                              | 명령어를 모를 때, 역할, 옵션 등을 알 수 있다.                                                                                                                                |                                                                                                                                                                                                    |
| npm 명령어 help  <br>npm install help    | 해당 명령어에 대해 알고 싶을 때                                                                                                                                          |                                                                                                                                                                                                    |
| npm install  <br>npm i                | npm 패키지 로컬 설치                                                                                                                                               | `--save` or `-S` : dependencies에 추가  <br>`--save-dev` or `-D` : devDependencies에 추가  <br>`-g` : global 패키지에 추가 (시스템 전체에서 사용할 수 있도록)<br>`--force` : 강제 재설치, 의존성에 충돌이 있거나, 호환되지 않는 버전이 있더라도 설치, 캐시무시 |
| npm uninstall moduleName              | npm 패키지 삭제                                                                                                                                                  |                                                                                                                                                                                                    |
| npm update                            | npm 패키지 업데이트 (필요한 모든 패키지)                                                                                                                                   | `npm update [package명]` : 특정 패키지만 업데이트                                                                                                                                                             |
| npm dedupe                            | 중복된 모듈 정리                                                                                                                                                   |                                                                                                                                                                                                    |
| npm root                              | node_modules의 위치를 알려준다.                                                                                                                                     |                                                                                                                                                                                                    |
| npm oudated                           | 오래된 패키지의 존재 유무를 알려준다.                                                                                                                                       |                                                                                                                                                                                                    |
| npm ls                                | 패키지를 조회한다.                                                                                                                                                  | npm ls 패키지명 해당 패키지의 유무, 어떤 패키지의 dependencies인지 알려준다.                                                                                                                                               |
| npm ll                                | 패키지의 더 자세한 정보를 알려준다.                                                                                                                                        |                                                                                                                                                                                                    |
| npm cache                             | npm내의 cache 목록 확인                                                                                                                                           | 캐시(Cache)                                                                                                                                                                                          |
| npm cache clean --force               | 캐시 삭제                                                                                                                                                       | 캐시(Cache)                                                                                                                                                                                          |
| npm rebuild                           | npm 재설치                                                                                                                                                     | 에러가 발생했을 때 주로 `npm cache clean`을 해주고                                                                                                                                                               |
| npm -v  <br>node -v  <br>npm -version | 버전 확인                                                                                                                                                       |                                                                                                                                                                                                    |
| npm install rimraf                    | rimraf 설치                                                                                                                                                   | node_modules 폴더 삭제 전                                                                                                                                                                               |
| rimraf node_modules                   | node_modules 폴더 삭제                                                                                                                                          | node_modules 폴더 삭제 실행                                                                                                                                                                              |
| npm prune                             | package.json 내에 정의 되지 않은 패키지 삭제                                                                                                                             |                                                                                                                                                                                                    |
| npm start                             | package.json의 scripts에 있는 start 명령어를 실행                                                                                                                     | 따로 설정하지 않았다면 node server.js가 실행                                                                                                                                                                    |
| npm stop                              | 실행중인 npm을 중지                                                                                                                                                |                                                                                                                                                                                                    |
| npm restart                           | stop후에 다시 start                                                                                                                                             |                                                                                                                                                                                                    |
| npm run                               | 그 이외의 scripts 실행                                                                                                                                            | `npm run 명령어`                                                                                                                                                                                      |
| npm config                            | npm의 설정을 조작                                                                                                                                                 | `npm config list` : 현재 설정 조회  <br>`npm set` : 이름 값  <br>`npm get` 이름 : 이름으로 속성을 설정하거나 조회                                                                                                           |
| npm list [package명]                   | 특정 패키지의 버전확인                                                                                                                                                |                                                                                                                                                                                                    |
| npm outdated                          | 프로젝트에 설치된 패키지 중, 버전관리가 필요한 패키지들 확인                                                                                                                          |                                                                                                                                                                                                    |
| npm list -a [package명]                | 프로젝트에 설치된 패키지의 의존성 확인                                                                                                                                       |                                                                                                                                                                                                    |
| npm ci                                | 1. package-lock.json 기반 개발환경과 동일한 패키지 버전을 보장받으며 설치<br>2. node_modules 디렉터리를 삭제하고 새로 설치함으로써 설치 중 문제가 발생하지 않도록 함<br>3. npm i보다 빠른 동작<br>4. 팀 프로젝트에서 일관된 환경 보장 |                                                                                                                                                                                                    |

#### 2. npx

- **npx**는 npm 버전 5.2.0 이후부터 포함된 도구로, **패키지를 설치하지 않고도 실행**할 수 있는 기능을 제공한다.
	- 임시로 패키지를 설치하고 실행하거나, 전역으로 설치된 실행 파일을 호출할 때 사용된다.

#### 주요 기능

1. **패키지 실행 (설치 없이)**
    - npx는 로컬 또는 전역에 패키지를 설치하지 않고, 필요한 패키지를 임시로 다운로드하여 실행할 수 있다.
```shell
npx <package-name>
```

2. **전역 패키지 실행**
    - 전역으로 설치하지 않아도 실행할 수 있다.
```shell
npx eslint <file>
```

3. **특정 버전 실행**
    - 특정 버전의 패키지를 실행할 수 있다.
```shell
npx <package-name>@<version>
```

4. **로컬 패키지 실행**
    - 프로젝트 내 설치된 패키지를 실행한다.
```shell
npx <local-package-name>
```


#### 3.  비교 및 적용 가능한 사례

| 특징       | npm                           | npx                           |
|----------|-------------------------------|-------------------------------|
| 역할       | 패키지 설치 및 관리, 스크립트 실행          | 패키지 실행 (임시 또는 로컬/전역 실행 파일 실행) |
| 설치 필요성   | 패키지를 설치해야 실행 가능               | 설치 없이도 실행 가능                  |
| 사용 예시    | npm install eslint → eslint . | npx eslint .                  |
| 특정 버전 실행 | 설치 후 사용 가능                    | 바로 특정 버전 실행 가능                |
| 편리성      | 장기적으로 사용하는 패키지 관리에 적합         | 단기적으로 사용하는 패키지 실행에 적합         |

##### npm 적용 가능 사례
- 프로젝트의 종속성 패키지를 설치하거나 업데이트할 때.
- 장기적으로 사용하는 개발 도구나 유틸리티를 설치할 때.
`npm install react npm install -g typescript`

##### npx 적용 가능 사례
- 한 번만 사용할 CLI 도구를 실행할 때.
- 특정 버전이나 실험적인 기능을 사용해야 할 때.
`npx create-react-app my-app npx json-server --watch db.json`