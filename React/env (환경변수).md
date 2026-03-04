
#### 1. 환경변수

- Next.js는 아래의 3가지 방식으로 환경변수 기능을 지원한다.
	1. `process.env.NODE_ENV`: 구동 환경 체크용 환경변수
	2. `.env` 파일 : 구동 환경별 환경변수 적용 파일
	3. `NEXT_PUBLIC_...`: 브라우저에서 참조하기 위한 Prefix

- Next.js는 내장된 `.env.local`에서 환경 변수를 `process.env`로 로드할 수 있는 기능을 제공한다.

- 기본적으로 환경 변수는 브라우저에 표시되지 않기 때문에 Node.js 환경에서만 사용할 수 있다.
	- 브라우저에 변수를 노출하려면 변수를 `NEXT_PUBLIC_`로 접두사(`prefix`)를 붙여 주어야 한다.

#### 2. `process.env.NODE_ENV`

- Next.js는 자동으로 `process.env.NODE_ENV` 변수를 생성하고, 구동 환경에 따라 아래의 3가지 값을 주입해 준다.
	 1. `development` : 개발 환경(`npm run dev`로 구동한 경우)
	 2. `production` : 배포 환경(`npm run build`로 빌드한 경우)
	 3. `test` : 테스트 환경

- `process.env.NODE_ENV`는 서버와 브라우저에서 모두 참조할 수 있다.
	- 주로 개발자 도구, 디버그/로그, 미들웨어 등을 설정할 때  사용한다.


#### 3. `.env`파일

- 개발자가 직접 필요한 환경변수를 정의하는 파일이다.
- `root` 경로에 아래 파일들을 생성해두면 구동 환경에 맞는 파일이 적용된다.

```null
1. **.env 파일**:
    - 모든 환경에서 default로 적용할 환경변수를 정의한다. 가장 우선순위가 낮다

2. **.env.production 파일**:
    - `.env.production` 파일은 배포/빌드와 같은 프로덕션 환경에서 사용된다.
    - 프로덕션 환경에서는 `.env.production` 파일을 사용하여 실제 서비스에 필요한 환경 변수를 설정한다.
    - 보안과 안정성을 위해 프로덕션 환경에서는 `.env.production` 파일을 조심스럽게 관리해야 한다.
 
3. **.env.development 파일**:
    - `.env.development` 파일은 개발 환경에서 사용된다.
    - 로컬 개발 환경에서 `.env` 파일 대신 `.env.development` 파일을 사용할 수도 있다.
    - 개발 환경에서는 테스트, 디버깅 등을 위해 특정 환경 변수가 설정될 수 있다.

4. **.env.test 파일**
	- 테스트 환경에서 적용된다.

5. **.env.local**
	- 가장 우선순위가 높다.
	- 다른 파일들에 정의된 값을 모두 override한다.
```

```null
1. .env 파일
- 가장 우선순위가 낮다. 모든 환경에서 공통으로 사용할 디폴트 키를 관리한다.
NEXT_PUBLIC_API_KEY=default_api_key
NEXT_VWORLD_LOCAL_SECRET=SECRET_KEY

2. .env.development 파일
- 개발환경(process.env.NODE_ENV === 'development')에서 사용할 키를 등록한다. 
- 개발환경일 경우, .env에 같은 환경변수가 있다면 덮어쓴다.
NEXT_PUBLIC_API_KEY=dev_api_key
NEXT_VWORLD_LOCAL_SECRET=SECRET_KEY

3. .env.production 파일
- 배포/빌드환경(process.env.NODE_ENV === 'production')에서 사용할 키를 등록한다. 
- 배포환경일 경우, .env에 같은 환경변수가 있다면 덮어쓴다.
NEXT_PUBLIC_API_KEY=prod_api_key
NEXT_VWORLD_LOCAL_SECRET=SECRET_KEY

4. .env.local 파일
- 모든 환경에서 최우선순위로 적용할 환경변수를 정의한다.
- 모든 .env.* 파일보다 우선순위가 높다.(같은 환경변수가 있다면 모두 덮어쓴다.)
NEXT_PUBLIC_API_KEY=local_api_key
NEXT_VWORLD_LOCAL_SECRET=SECRET_KEY
```


#### 4. `NEXT_PUBLIC_`

- 기본적으로 환경변수는 서버에서만 참조할 수 있다.
	- 만약, 서버 및 브라우저 모두에서 환경변수를 사용하고 싶다면, 변수명 앞에 `NEXT_PUBLIC` 이라는 `prefix`를 붙여줘야 한다.

- 즉, 환경변수 생성 시, `NEXT_PUBLIC_[VAL]`형태로 생성하면 된다.
	- 환경변수 참조 시에도, `NEXT_PUBLIC_[VAL]` 으로 사용해야 한다.

```tsx
// NEXT_PUBLIC_ prefix 미사용 시, 해당 변수는 브라우저에서 참조 불가능.
console.log(process.env.NEXT_VWORLD_LOCAL_SECRET); // undefined

// NEXT_PUBLIC_ prefix 사용 시, 해당 변수는 브라우저에서도 참조 가능
console.log(process.env.NEXT_PUBLIC_API_KEY); //default_api_key
```


#### 5. 참고문서

- [공식 참고문서](https://nextjs.org/docs/pages/building-your-application/configuring/environment-variables)
- [포스트](https://curryyou.tistory.com/503)
