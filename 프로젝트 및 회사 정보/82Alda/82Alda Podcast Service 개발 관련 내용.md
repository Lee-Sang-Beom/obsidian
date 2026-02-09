
#### 1. Git Repository
- GIT: http://dev.ijaksnc.co.kr/Alda_Podcast/alda_podcast_frontend/code/develop

#### 2. Swagger-UI
- 팟캐스트 Service Swagger UI: [Swagger UI](http://192.168.1.13:7581/webjars/swagger-ui/index.html)
- 팟캐스트 실시간 정보 Swagger UI : [Swagger UI](https://apidev.82alda.co.kr:4000/api-docs)

#### 3. jenkins 주소 및 계정 정보
- jenkins 주소: [http://192.168.1.225:9800/ ](http://192.168.1.11:7500/)
```
[id / password]
admin / dkdlwkr
```

#### 4. 개발 및 상용 환경주소 정보
- 개발환경 홈페이지: http://192.168.1.13:7580/
- 상용환경 홈페이지: https://podcast.82alda.co.kr/
```
[id / password]
free@email.com / dkdlwkr
basic@email.com / dkdlwkr
premium@email.com / dkdlwkr
```

#### 5. 개발환경 MobaXTerm Docker 재배포 명령어
- 세션접속: 192.168.1.13
	- 사용자명: ijak
	- 포트 22 그대로
	- 비밀번호: dkdlwkr

- 명령어
```
cd /ijak_home/docker-work/alda_podcast_frontend_dev/
dockc down << 서버끔
dockc up -d << 서버킴
```

#### 6. 상용환경 MobaXTerm Docker 재배포 명령어
- 세션접속: 43.201.23.183
	- 사용자명: ubuntu
	- 포트 22 그대로
	-  SSH -> Advanced SSH Settings -> Use private key가 C드라이브에 alda-podcast-ijak.pem

- 명령어
```
cd ./docker-work/alda_podcast_frontend_prod
dockc down << 서버끔
dockc up -d << 서버킴
```

#### 7. 내 인터넷 프로토콜 버전 설정

![[IP설정.png]]
