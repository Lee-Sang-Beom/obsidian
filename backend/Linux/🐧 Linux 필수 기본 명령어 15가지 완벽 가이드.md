
> **💡 중요**: Linux를 사용하는 데 필요한 기본 명령 15가지는 필수 명령입니다. 반드시 익히고 넘어가세요!
---

## 📁 파일 및 디렉터리 탐색

### 1. `ls` - 파일 목록 보기

**List**의 약자로 Windows 명령 프롬프트의 `dir` 명령과 같은 역할을 합니다.  
해당 디렉터리(폴더)에 있는 파일의 목록을 나열하는 명령입니다.

> **📋 ls 사용법**
```bash
> ls                          # 현재 디렉터리의 파일 목록 표시
> ls /etc/sysconfig           # 특정 디렉터리의 목록 표시  
> ls -a                       # 숨김 파일까지 모두 표시
> ls -l                       # 파일 정보를 자세히 표시
> ls *.cfg                    # 확장자가 cfg인 파일만 표시
> ls -l /etc/sysconfig/a*     # a로 시작하는 파일들의 상세 정보 표시
> ```

### 2. `cd` - 디렉터리 이동

**Change Directory**의 약자로, 디렉터리 이동을 위한 명령입니다.

> **🚀 cd 사용법**
```bash
> cd                          # 현재 사용자의 홈 디렉터리로 이동
> cd ~userName                # 특정 사용자의 홈 디렉터리로 이동
> cd ..                       # 상위(부모) 디렉터리로 이동
> cd /etc/sysconfig          # 절대 경로로 이동
> cd ../etc/sysconfig        # 상대 경로로 이동
> ```

### 3. `pwd` - 현재 위치 확인

**Print Working Directory**의 약자로, 현재 디렉터리의 전체 경로를 화면에 표시합니다.

> **📍 pwd 사용법**
```bash
> pwd                         # 현재 작업 중인 디렉터리의 경로 출력
> ```

---

## 🗂️ 파일 및 디렉터리 관리

### 4. `rm` - 파일/디렉터리 삭제

파일이나 디렉터리를 삭제합니다. 디렉터리 삭제 권한이 있어야 해당 명령을 실행할 수 있습니다.

> **⚠️ rm 사용법 (주의 필요!)**
```bash
> rm abc.txt                  # 파일 삭제
> rm -i abc.txt              # 삭제 전 확인 메시지 표시
> rm -f abc.txt              # 강제 삭제 (확인 없이)
> rm -r abc                  # 디렉터리 삭제
> rm -rf abc                 # 디렉터리 및 하위 모든 내용 강제 삭제 ⚠️
> ```

### 5. `cp` - 파일/디렉터리 복사

파일이나 디렉터리를 복사하기 위한 명령입니다.  
새로 복사한 파일은 복사한 사용자의 소유가 되므로, 해당 파일의 읽기 권한이 필요합니다.

> **📋 cp 사용법**
```bash
> cp abc.txt cba.txt         # abc.txt를 cba.txt로 복사
> cp -r abc cda              # abc 디렉터리를 cda로 복사
> ```

### 6. `touch` - 파일 생성/시간 수정

크기가 0인 새 파일을 생성하거나, 기존 파일의 최종 수정 시간을 변경합니다.

> **✨ touch 사용법** 
```bash
> touch abc.txt              # 새 파일 생성 또는 수정 시간 갱신
> ```

### 7. `mv` - 파일 이동/이름 변경

**Move**의 약자로, 파일이나 디렉터리의 이름을 변경하거나 다른 디렉터리로 옮길 때 사용합니다.

> **🔄 mv 사용법** 
```bash
> mv abc.txt /etc/sysconfig/ # abc.txt를 /etc/sysconfig/ 디렉터리로 이동
> mv aaa bbb                 # aaa 파일을 bbb로 이름 변경
> mv abc.txt www.txt         # abc.txt를 www.txt로 이름 변경
> ```

### 8. `mkdir` - 디렉터리 생성

**Make Directory**의 약자로, 새로운 디렉터리를 생성합니다.  
생성된 디렉터리는 명령을 실행한 사용자의 소유가 됩니다.

> **📁 mkdir 사용법**
```bash
> mkdir abc                  # 현재 디렉터리에 abc 디렉터리 생성
> mkdir -p /def/fgh          # 중간 디렉터리가 없으면 자동 생성하며 경로 생성
> ```

### 9. `rmdir` - 빈 디렉터리 삭제

**Remove Directory**의 약자로, 기존 디렉터리를 삭제합니다.  
해당 디렉터리의 삭제 권한이 있어야 하며 **디렉터리는 반드시 비어 있어야** 합니다.

> **🗑️ rmdir 사용법**
```bash
> rmdir abc                  # 빈 abc 디렉터리 삭제
> ```

---

## 📖 파일 내용 보기

### 10. `cat` - 파일 내용 전체 출력

파일의 내용을 화면에 전체 출력합니다.

> **📄 cat 사용법**
```bash
> cat a.txt                  # a.txt 파일 내용을 화면에 출력
> ```

### 11. `head` / `tail` - 파일 앞부분/뒷부분 보기

텍스트 형식으로 작성된 파일의 **맨 앞 10행** 혹은 **맨 뒤 10행**만 화면에 출력합니다.

> **🔝🔚 head/tail 사용법** 
```bash
> head anaconda-ks.cfg       # 파일의 앞 10행 출력
> head -3 anaconda-ks.cfg    # 파일의 앞 3행만 출력
> tail -5 anaconda-ks.cfg    # 파일의 맨 뒤 5행만 출력
> tail -f error.log          # 실시간으로 파일 끝부분 모니터링
> ```

### 12. `more` - 페이지별 파일 보기

텍스트 형식으로 작성된 파일을 페이지 단위로 화면에 출력합니다.

> **📚 more 사용법 및 조작키**
```bash
> more anaconda-ks.cfg       # 파일 내용을 페이지별로 출력
> more +30 anaconda-ks.cfg   # 30행부터 출력 시작
> ```


> **키보드 조작**:
> - `[Space]`: 다음 페이지
> - `[B]`: 이전 페이지
> - `[Q]`: 종료

### 13. `less` - 향상된 페이지별 파일 보기

`more` 명령과 용도가 비슷하지만 기능이 더 확장되었습니다.  
화살표 키, `[PageUp]`, `[PageDown]` 키도 사용할 수 있습니다.

> **📖 less 사용법**
```bash
> less anaconda-ks.cfg       # 파일 내용을 향상된 뷰어로 출력
> less +30 anaconda-ks.cfg   # 30행부터 출력 시작
> ```

> **추가 키보드 조작**: 
> - `↑↓←→`: 방향키로 이동
> - `[PageUp/PageDown]`: 페이지 단위 이동
> - `/검색어`: 내용 검색
> - `[Q]`: 종료

---

## 🔍 시스템 정보 및 유틸리티

### 14. `file` - 파일 종류 확인

파일의 종류를 표시합니다.

> **🔎 file 사용법**
 ```bash
> file anaconda-ks.cfg       # 텍스트 파일이면 ASCII로 표시
> file /dev/sr0              # DVD 장치이면 block special로 표시
> file image.jpg             # 이미지 파일 종류 확인
> ```

### 15. `clear` - 화면 지우기

현재 사용 중인 터미널 화면을 깨끗하게 지웁니다.

> **🧹 clear 사용법**
```bash
> clear                      # 터미널 화면 지우기
> ```
 
> **💡 팁**: `Ctrl + L` 단축키로도 같은 효과를 얻을 수 있습니다!

---

## 📚 학습 팁

### 🎯 효과적인 학습 순서

1. **기본 탐색**: `ls`, `cd`, `pwd` → 파일시스템 이해
2. **파일 관리**: `cp`, `mv`, `rm` → 기본 파일 조작
3. **디렉터리 관리**: `mkdir`, `rmdir` → 폴더 구조 관리
4. **파일 내용**: `cat`, `head`, `tail`, `more`, `less` → 내용 확인
5. **유틸리티**: `touch`, `file`, `clear` → 보조 기능

### ⚡ 자주 사용하는 조합

```bash
# 디렉터리 생성 후 이동
mkdir project && cd project

# 파일 생성 후 내용 확인
touch test.txt && ls -l test.txt

# 안전한 파일 삭제 (확인 후)
rm -i important_file.txt

# 실시간 로그 모니터링
tail -f /var/log/system.log
```

### 🚨 주의사항

- `rm -rf` 명령어는 매우 위험합니다. 신중하게 사용하세요!
- 중요한 파일은 삭제 전에 반드시 백업하세요
- 권한이 없는 파일/디렉터리 작업시 `sudo`가 필요할 수 있습니다
