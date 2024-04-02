
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
2. `http://wisdom.lksmart.co.kr/`가 리버스 프록시를 탐(주소로)
	- http: 80이고, https:443으로 자동 매치되서 listen
![[Pasted image 20240402141720.png]]
5. NAS의 리버스 프록시는 요청을 보고, proxy_pass에 연결된 내부 IP인 (10.10.10.213:13030)로 요청
	- 프록시는 내부 IP로 요청이 가능함

6. 213(뎁스사설  ip) 서버로 내부 IP 요청이 들어오면, nginx서버 `listen:13030`에 설정된 리버스 프록시를 한 번 더 탐
		- 213서버의 3031포트로 이동시킴(docker) -> proxy_pass

1. 특정 docker container의 외부 포트에 요청이 들어오면 해당 포트를 사용하고 있는 컨테이너와 연결 되어 있는 내부 포트를 사용하는 컨테이너 내부 서비스에 요청이 전해짐 (:3000)

1. 프론트의 Next.js 3000번 포트에까지 요청이 전해짐


```null

server{
	listen: [포트번호]
	...
}
```