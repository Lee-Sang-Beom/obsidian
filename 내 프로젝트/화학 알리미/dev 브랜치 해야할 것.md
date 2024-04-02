
- 공통
	- localhost 미포함 시, `/cwchemical` path 맨 앞에 추가

1. `api` 주소문자열 감싸는 func 하나 만들고 모두 적용 (serversidefetch 제외)
2. dev `public/cwchemial` `app/cwchemical/...` 디렉터리 구조 변경
3. `sessionProvider` basepath 변경
	1. dev (/cwchemical 추가)
	2. local (내버려둠)

1. `dev` -> `assertPrefix`
2. `api key (vworld)`
