
#### 공통
- `localhost` 미포함 시, `/cwchemical` path 맨 앞에 추가

#### 작업

- `api` 주소문자열 감싸는 func 하나 만들고 모두 적용 (serversidefetch 제외)
- dev `public/cwchemial` `app/cwchemical/...` 디렉터리 구조 변경
- `sessionProvider` basepath 변경
	1. dev (/cwchemical 추가)
	2. local (내버려둠)

- `dev` -> `assertPrefix` 삭제
- `api key (vworld)`

#### 학습

1. `http://wisdom.lksmart.co.kr/` 접속
2. 네임서버에 의해, `http://wisdom.lksmart.co.kr/`가 뎁스의 공인 IP(106.241.188.226)로 변환
3. NAS에 있는 리버스 프록시를 탐
4. NAS의 리버스 프록시는 요청을 보고, proxy_pass에 연결된 내부 IP인 (10.10.10.213:13030)로 요청
	- 프록시는 내부 IP로 요청이 가능함
5. 213서버로 내부 IP 요청이 들어오면, nginx서버 `listen:13030`에 설정된 리버스 프록시를 한 번 더 탐
6. 특정 docker container의 외부 포트에 요청이 들어오면 해당 포트를 사용하고 있는 컨테이너와 연결 되어 있는 내부 포트를 사용하는 컨테이너 내부 서비스에 요청이 전해짐 (:3000)
7. 프론트의 Next.js 3000번 포트에까지 요청이 전해짐