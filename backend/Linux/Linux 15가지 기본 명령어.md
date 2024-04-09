- Linux를 사용하는 데 필요한 기본 명령 15가지는 필수 명령이다. 반드시 익히고 넘어가자.

#### 1. `ls`
 - List의 약자로 Windows 명령 프롬프트의 `dir` 명령과 같은 역할을 한다. 
 - 대체적으로, 해당 디렉터리(폴더) 에 있는 파일의 목록을 나열하는 명령이다.

> [!note] ls
> - `ls`: 현재 디렉터리의 파일 목록 표시
> - `ls /etc/sysconfig /etc/sysconfig`: 디렉터리의 목록 표시
> - `ls -a`: 현재 디렉터리의 목록 표시 (숨김 파일 표시)
> - `ls -l`: 현재 디렉터리의 목록을 자세히 표시
> - `ls *.cfg`: 확장자가 cfg인 목록 표시
> - `ls -l/etc/sysconfig/a*`: `/etc/sysconfig` 디렉터리 중 앞글자가 `a*`인 디렉터리의 목록을 표시 (숨김 파일 표시)

#### 2. `cd`
- 디렉터리 이동을 위한 명령이다.

> [!note] cd
> - `cd`: 현재 사용자의 홈 디렉터리로 이동 (만약, 현재 사용자가 `root`이면, `/root` 디렉터리로 이동)
> - `cd ~userName userName`: 사용자의 홈 디렉터리로 이동
> - `cd ..`: 상위(부모) 디렉터리로 이동
> - `cd /etc/sysconfig`: 절대 경로로 `/etc/sysconfig` 디렉터리로 이동
> - `cd ../etc/sysconfig`": 상대 경로로 `/etc/sysconfig` 디렉터리로 이동

#### 3. `pwd`
- 현재 디렉터리의 전체 경로를 화면에 표시한다.

> [!note] pwd
> - `pwd`: 현재 작업 중인 디렉터리의 경로 출력

#### 4. `rm`
- 파일이나 디렉터리를 삭제한다.
- 디렉터리 삭제 권한이 있어야 해당 명령을 실행 가능하다.

> [!note] rm
> - `rm abc.txt`: 해당 파일 삭제
> - `rm -i abc.txt`: 삭제 시, 정말 삭제할 지 확인하는 메시지를 표시
> - `rm -f abc.txt`: 삭제 시, 확인하지 않고 강제(force)삭제
> - `rm -r abc`: 해당 (abc) 디렉터리 삭제
> - `rm -rf abc`: 해당 (abc) 디렉터리 및 하위 디렉터리를 강제로 모두 삭제 (주의 필요)

#### 5. `cp`
- 파일이나 디렉터리를 복사하기 위한 명령이다.
- 새로 복사한 파일은 복사한 사용자의 소유가 되므로, 명령을 실행하는 사용자는 해당 파일의 읽기 권한을 필요로 한다.

> [!note] cp
> - `cp abc.txt cba.txt`: 현재, `abc.txt`라는 파일을 `cba.txt`라는 이름으로 변경하여 복사
> - `cp -r abc cda`: `abc`디렉터리를 `cda`라는 디렉터리라는 이름으로 복사

#### 6. `touch`
- 크기가 0인 새 파일을 생성하거나, 생성된 파일이 존재한다면 파일의 최종 수정 시간을 변경한다.

> [!note] touch
> - `touch abc.txt`: 파일이 없는 경우, `abc.txt` 파일을 생성하고, 이미 존재하는 경우 최종 수정 시각을 현재 시각으로 변경

#### 7. `mv`
- `move`의 약자로, 파일이나 디렉터리의 이름을 변경하거나, 다른 디렉터리로 옮길 때 사용하는 명령이다.

> [!note] mv
> - `mv abc.txt /etc/sysconfig/`: `abc.txt`를 `/etc/sysconfig/` 디렉터리로 이동
> - `mv aaa bbb`: `aaa` 파일을 `bbb` 파일로 변경
> - `mv abc.txt www.txt`: `abc.txt`파일 이름을 `www.txt`로 변경

#### 8. `mkdir`
- `make directory`의 약자로, 새로운 디렉터리를 생성한다.
- 른 디렉터리로 옮길 때 사용하는 명령이다.

> [!note] mv
> - `mv abc.txt /etc/sysconfig/`: `abc.txt`를 `/etc/sysconfig/` 디렉터리로 이동
> - `mv aaa bbb`: `aaa` 파일을 `bbb` 파일로 변경
> - `mv abc.txt www.txt`: `abc.txt`파일 이름을 `www.txt`로 변경


