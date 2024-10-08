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
- 생성된 디렉터리는 명령을 실행한 사용자의 소유가 된다.

> [!note] mkdir
> - `mkdir abc`: 현재 디렉터리 아래에 `/abc` 이름의 디렉터리 생성
> - `mkdir -p /def/fgh`: `/def/fgh` 디렉터리를 생성한다. 만약, `/def`디렉터리가 없으면 자동 생성
> 	- `-p`옵션은 필요한 경우 중간 디렉터리를 생성하도록 지시하도록 한다.

#### 9. `rmdir`
- `remove directory`의 약자로, 기존 디렉터리를 삭제한다.
- 해당 디렉터리의 삭제 권한이 있어야 하며 디렉터리는 비어 있어야 한다.
- 파일이 있는 디렉터리를 삭제하려면 `rm -r` 명령을 실행해야 한다.

> [!note] rmdir
> - `rmdir abc`: `/abc`디렉터리 삭제

#### 10. `cat`
- 파일의 내용을 화면에 출력한다

> [!note] cat
> - `cat a.txt`: `a.txt`파일 내용을 화면에 출력한다.

#### 11. `head, tail`
- 텍스트 형식으로 작성된 파일의 **맨 앞 10행** 혹은 **맨 뒤 10행**만 화면에 출력한다.

> [!note] head, tail
> - `head anaconda-ks.cfg`: 해당 파일의 앞 10행을 화면에 출력
> - `head -3 anaconda-ks.cfg`: 해당 파일의 앞 3행만 화면에 출력
> - `tail -5 anaconda-ks.cfg`: 해당 파일의 맨 뒤 5행만 화면에 출력

#### 12. `more`
- 텍스트 형식으로 작성된 파일을 페이지 단위로 화면에 출력한다.
- `[Space]`를 누르면, 다음 페이지로 이동하며, `[B]`를 누르면 앞 페이지로 이동한다.
- `[Q]`를 누르면 명령을 종료한다.

> [!note] more
> - `more anaconda-ks.cfg`: 파일 내용을 터미널에 출력
> - `more +30 anaconda-ks.cfg`: 파일 내용을 30줄부터 출력

#### 13. `less`
- `more` 명령과 용도가 비슷하지만, 기능이 더 확장되었다.
- `more`에서 사용하는 키와 더불어 화살표 키나 `[PageUp]`, `[PageDown]` 키도 사용할 수 있다.

> [!note] less
> - `less anaconda-ks.cfg`: 파일 내용을 터미널에 출력
> - `more +30 anaconda-ks.cfg`: 파일 내용을 30줄부터 출력

#### 14. `file`
- 파일의 종류를 표시한다.

> [!note] file
> - `file anaconda-ks.cfg`: `anaconda-ks.cfg`는 텍스트 파일이기 때문에, 아스키 파일(ASCII)로 표시된다.
> - `file /dev/sr0`: `sr0`은 DVD 장치이므로, block special로 표시된다.

#### 15. `clear`
- 현재 사용 중인 터미널 화면을 지운다.

> [!note] clear
> - `clear`
