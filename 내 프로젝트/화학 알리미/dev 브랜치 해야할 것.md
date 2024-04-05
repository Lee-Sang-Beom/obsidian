
#### 공통
- `localhost` 미포함 시, `/cwchemical` path 맨 앞에 추가

#### 작업

- `api` 주소문자열 감싸는 func 하나 만들고 모두 적용 (serversidefetch 제외)
	-`main`: `cwchemical` 추가
	-`dev`: `cwchemical` 추가
	-`localhost`: `cwchemical` 추가
- dev `public/cwchemial` `app/cwchemical/...` 디렉터리 구조 변경
- `sessionProvider` basepath 변경 (OK)


- `dev` -> `assertPrefix` 삭제
- `api key (vworld)`

[중요]
#### 완료
1. sessionProvider basePath 변경
2. app 하위요소 및 이미지 접근경로 cwchemical 추가
3. 로컬 및 dev환경 `assertPrefix` 삭제
4. `api key`