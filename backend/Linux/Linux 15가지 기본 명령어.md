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
> - `cd ~rocky rocky`: 사용자의 홈 디렉터리로 이동
> - `cd ..`: 상위(부모) 디렉터리로 이동
> - `cd /etc/sysconfig`: 절대 경로로 `/etc/sysconfig` 디렉터리로 이동
> - `cd ../etc/sysconfig`": 상대 경로로 `/etc/sysconfig` 디렉터리로 이동

#### 3. `pwd`
- 디렉터리 이동을 위한 명령이다.

> [!note] cd
> - `cd`: 현재 사용자의 홈 디렉터리로 이동 (만약, 현재 사용자가 `root`이면, `/root` 디렉터리로 이동)
> - `cd ~rocky rocky`: 사용자의 홈 디렉터리로 이동
> - `cd ..`: 상위(부모) 디렉터리로 이동
> - `cd /etc/sysconfig`: 절대 경로로 `/etc/sysconfig` 디렉터리로 이동
> - `cd ../etc/sysconfig`": 상대 경로로 `/etc/sysconfig` 디렉터리로 이동




