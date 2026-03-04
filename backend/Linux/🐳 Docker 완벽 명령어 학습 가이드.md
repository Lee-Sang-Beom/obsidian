> **💡 핵심**: Docker는 Linux 기반이므로 앞서 배운 Linux 명령어들을 컨테이너 안에서도 그대로 사용합니다!

---

## 🚀 1. Docker 기본 명령어

### 📦 컨테이너 관리 (Container Management)

> **🔄 컨테이너 생명주기 관리**
```bash
> docker run [옵션] 이미지명              # 새 컨테이너 실행
> docker start 컨테이너ID_또는_이름       # 중지된 컨테이너 시작
> docker stop 컨테이너ID_또는_이름        # 실행 중인 컨테이너 중지
> docker restart 컨테이너ID_또는_이름     # 컨테이너 재시작
> docker rm 컨테이너ID_또는_이름          # 컨테이너 삭제
> ```

> **👀 컨테이너 상태 확인**
```bash
> docker ps                              # 실행 중인 컨테이너 목록
> docker ps -a                           # 모든 컨테이너 목록 (중지된 것 포함)
> docker exec -it 컨테이너명 /bin/bash    # 실행 중인 컨테이너에 접속
> ```

### 🖼️ 이미지 관리 (Image Management)

> **📥📤 이미지 다운로드 및 관리**
```bash
> docker images                          # 로컬 이미지 목록 보기
> docker pull 이미지명:태그               # 이미지 다운로드
> docker rmi 이미지ID_또는_이름           # 이미지 삭제
> docker build -t 이미지명:태그 .         # Dockerfile로 이미지 빌드
> docker image prune                     # 사용하지 않는 이미지 정리
> ```

### 📊 로그 및 상태 확인 (Monitoring & Debugging)

> **🔍 컨테이너 모니터링**
 ```bash
> docker logs 컨테이너명                  # 컨테이너 로그 보기
> docker logs -f 컨테이너명               # 실시간 로그 보기
> docker inspect 컨테이너명               # 컨테이너 상세 정보
> docker info                            # Docker 시스템 정보
> docker version                         # Docker 버전 확인
> ```

---

## 🎼 2. Docker Compose 명령어

### ⚡ 기본 조작 (Basic Operations)

> **🎯 서비스 생명주기 관리**
```bash
> docker-compose up -d                   # 서비스 시작 (백그라운드)
> docker-compose up                      # 서비스 시작 (포그라운드)
> docker-compose down                    # 서비스 중지 및 컨테이너 제거
> docker-compose stop                    # 서비스 중지만 (컨테이너 유지)
> docker-compose start                   # 중지된 서비스 시작
> docker-compose restart                 # 서비스 재시작
> ```

### 📈 상태 확인 및 관리 (Status & Management)

> **📋 서비스 모니터링**
```bash
> docker-compose ps                      # 실행 중인 서비스 상태 보기
> docker-compose logs                    # 모든 서비스 로그 보기
> docker-compose logs 서비스명           # 특정 서비스 로그 보기
> docker-compose logs -f                 # 실시간 로그 보기
> docker-compose up -d --scale 서비스명=개수  # 서비스 스케일링
> ```

### 🔨 빌드 및 정리 (Build & Cleanup)

> **🏗️ 빌드 및 정리 작업**
 ```bash
> docker-compose build                   # 이미지 빌드
> docker-compose up -d --build           # 빌드와 함께 서비스 시작
> docker-compose down -v                 # 볼륨까지 함께 삭제
> docker-compose down --rmi all          # 이미지까지 함께 삭제
> ```

---

## ⚙️ 3. 실용적인 Docker 옵션들

### 🎮 docker run 주요 옵션

> **🚀 컨테이너 실행 옵션들**
```bash
> docker run -d 이미지명                           # 백그라운드 실행
> docker run -p 8080:80 이미지명                   # 포트 매핑 (호스트:컨테이너)
> docker run -v /host/path:/container/path 이미지명 # 볼륨 마운트
> docker run -e ENV_VAR=value 이미지명             # 환경변수 설정
> docker run --name my-container 이미지명          # 컨테이너 이름 지정
> docker run -it 이미지명 /bin/bash                # 인터랙티브 모드 (터미널)
> docker run --rm 이미지명                         # 종료 시 자동 삭제
> ```

### 🎯 실전 조합 예시

> **💪 강력한 조합 명령어들**
```bash
> # 개발 환경: 포트 매핑 + 볼륨 마운트 + 환경변수
> docker run -d -p 3000:3000 -v $(pwd):/app -e NODE_ENV=development node:18
> 
> # 디버깅: 인터랙티브 모드로 컨테이너 내부 확인
> docker run -it --rm ubuntu:20.04 /bin/bash
> 
> # 데이터베이스: 볼륨 + 환경변수 + 이름 지정
> docker run -d --name mysql-db -p 3306:3306 -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret mysql:8.0
> ```

---

## 🔧 4. 자주 사용하는 조합 명령어

### 💻 개발 환경 관리 (Development Workflow)

> **🔄 개발 워크플로우**
```bash
> # 개발 서버 완전 재시작 (깨끗한 상태로)
> docker-compose down && docker-compose up -d
> 
> # 특정 서비스만 재시작 (빠른 재시작)
> docker-compose restart web
> 
> # 캐시 무시하고 새로 빌드 (코드 변경 후)
> docker-compose build --no-cache
> 
> # 특정 서비스 실시간 로그 모니터링
> docker-compose logs -f api
> ```

### 🧹 정리 및 관리 (Cleanup & Maintenance)

> **🗑️ 시스템 정리 명령어들**
```bash
> docker container prune                 # 중지된 모든 컨테이너 삭제
> docker system prune                    # 사용하지 않는 모든 리소스 정리
> docker volume prune                    # 사용하지 않는 볼륨 정리
> docker network prune                   # 사용하지 않는 네트워크 정리
> docker system prune -a --volumes       # 모든 것 정리 (신중히 사용!)
> ```

---

## ⚡ 5. 유용한 별칭(Alias) 설정

**🔧 .bashrc나 .zshrc에 추가하면 편리한 별칭들:**

> **🐳 Docker 별칭**
```bash
> # Docker 기본 명령어 단축
> alias dk='docker'
> alias dkps='docker ps'
> alias dkpsa='docker ps -a'
> alias dki='docker images'
> alias dkrm='docker rm'
> alias dkrmi='docker rmi'
> 
> # Docker Compose 별칭  
> alias dkc='docker-compose'
> alias dkcu='docker-compose up -d'
> alias dkcd='docker-compose down'
> alias dkcl='docker-compose logs -f'
> alias dkcps='docker-compose ps'
> alias dkcb='docker-compose build'
> 
> # 자주 사용하는 조합
> alias dkrestart='docker-compose down && docker-compose up -d'
> alias dkclean='docker system prune -f'
> ```

---

## 📚 6. 체계적인 학습 순서

### 🎯 단계별 학습 로드맵

> **1️⃣ 기초 단계** - Docker 기본 개념 익히기
```bash
> docker run hello-world                 # Docker 설치 확인
> docker run -it ubuntu /bin/bash        # 컨테이너 내부 탐험
> docker ps, docker ps -a               # 컨테이너 상태 확인
> ```

> **2️⃣ 초급 단계** - 컨테이너 조작 마스터
```bash
> docker run, docker stop, docker start  # 생명주기 관리
> docker images, docker pull, docker rmi # 이미지 관리
> docker logs, docker exec -it          # 디버깅 기본
> ```

> **3️⃣ 중급 단계** - Docker Compose 활용
```bash
> docker-compose up/down                 # 멀티 컨테이너 관리
> docker-compose logs, docker-compose ps # 서비스 모니터링
> docker-compose.yml 작성               # 설정 파일 이해
> ```

> **4️⃣ 고급 단계** - 실무 스킬 습득
```bash
> docker run -p, -v, -e                 # 고급 옵션 활용
> docker build, Dockerfile 작성         # 커스텀 이미지 생성
> docker prune 계열                     # 시스템 관리
> ```

---

## 🛠️ 7. 실습 팁 및 베스트 프랙티스

### 🎓 초보자를 위한 실습 가이드

> **🚀 첫 실습 추천 순서**
> 1. **Hello World로 시작**: `docker run hello-world`
> 2. **웹서버 실행**: `docker run -d -p 8080:80 nginx`
> 3. **컨테이너 내부 탐험**: `docker exec -it container_name /bin/bash`
> 4. **간단한 docker-compose.yml 작성**: nginx + mysql 조합

> **💡 유용한 팁들**
```bash
> # 도움말은 언제든지
> docker --help
> docker run --help
> docker-compose --help
> 
> # 컨테이너 ID는 앞 3-4자리만 써도 OK
> docker stop abc123  # abcdef123456... 대신
> 
> # 탭 자동완성 활용하기
> docker ps [Tab][Tab]  # 옵션들이 나타남
> ```

### ⚠️ 주의사항 및 안전 수칙

> **🚨 위험한 명령어들 (신중히 사용!)**
```bash
> docker system prune -a --volumes       # 모든 데이터 삭제 위험!
> docker rm -f $(docker ps -aq)         # 모든 컨테이너 강제 삭제
> docker rmi -f $(docker images -q)     # 모든 이미지 강제 삭제
> ```

> **✅ 안전한 사용 습관**
> - 중요한 데이터는 항상 볼륨으로 백업
> - `docker-compose down -v` 사용 시 데이터 손실 주의
> - 프로덕션 환경에서는 `--rm` 옵션 신중히 사용
> - 정기적으로 `docker system df`로 디스크 사용량 확인

---

## 🎯 8. 실무에서 자주 사용하는 패턴

### 🔥 개발자들이 가장 많이 쓰는 명령어 TOP 10

> **⭐ 실무 필수 명령어들**
```bash
> 1. docker-compose up -d                # 개발 환경 시작
> 2. docker-compose logs -f service명    # 실시간 로그 확인  
> 3. docker exec -it container명 bash    # 컨테이너 디버깅
> 4. docker-compose down                 # 환경 정리
> 5. docker ps                          # 상태 확인
> 6. docker-compose restart service명    # 서비스 재시작
> 7. docker logs -f container명          # 로그 모니터링
> 8. docker-compose build --no-cache     # 강제 리빌드
> 9. docker system prune                # 공간 정리
> 10. docker-compose up -d --build       # 빌드 후 시작
> ```
