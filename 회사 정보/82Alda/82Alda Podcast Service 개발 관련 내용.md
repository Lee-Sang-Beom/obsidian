
![[IP설정.png]]

#### 홈페이지
- 팟캐스트 GIT: [Alda_Podcast](http://dev.ijaksnc.co.kr/organizations/Alda_Podcast)
- 팟캐스트 v0.dev: [Fork of Korean podcast page - Dev Final – v0 by Vercel](https://v0.dev/chat/fork-of-korean-podcast-page-dev-final-1p3twpdaDi8)
- 오디오 개발: [82ALDA Podcast - 오디오 플레이어](http://52.79.86.180:3100/test/audio)

#### Swagger
- 팟캐스트 Service Swagger UI: [Swagger UI](http://192.168.1.13:7581/webjars/swagger-ui/index.html)
- 팟캐스트 Real-Time Swagger UI (실시간): [Swagger UI](https://apidev.82alda.co.kr:4000/api-docs)

#### 접속 및 빌드 정보
- jenkins 주소: [Dashboard [Jenkins]](http://192.168.1.11:7500/) (계정정보: admin, dkdlwkr)
- 개발환경 홈페이지: http://192.168.1.13:7580/
- 상용환경 홈페이지: https://podcast.82alda.co.kr/
- 로그인: basic@email.com, free@email.com, premium@email.com

#### mobaxterm (82alda podcast service 개발 환경)
- 세션접속: 192.168.1.13
	- 사용자명: ijak
	- 포트 22 그대로
	- 비밀번호: dkdlwkr
- 위치
	- cd /ijak_home/docker-work/alda_podcast_frontend_dev/
	- dockc down << 서버끔
	- dockc up -d << 서버킴

#### mobaxterm (82alda podcast service 상용 환경)
- 세션접속: 43.201.23.183
	- 사용자명: ubuntu
	- 포트 22 그대로
	-  SSH -> Advanced SSH Settings -> Use private key가 C드라이브에 alda-podcast-ijak.pem

- 위치
	- cd ./docker-work/alda_podcast_frontend_prod
	- dockc down << 서버끔
	- dockc up -d << 서버킴