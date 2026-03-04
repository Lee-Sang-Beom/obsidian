
GitLab, Jenkins는 CI/CD의 도구이다.

CI/CD에 대해 알아보자.

### CI/CD

CI(지속적 통합, Continuous Integration)란?

- 소스코드를 자동으로 자주 통합시키는 것

CD(지속적 제공/배포, Continuous Delivery/Deployment)란?

코드의 변경 사항의 테스트 제공 및 자동 배포를 뜻한다.

즉, CI/CD는 소스코드를 관리하면서, 해당 소스 코드에 변경이 일어나면 자동으로 테스트 환경을 제공하고 배포하는 프로세스를 의미한다고 생각하자.

뎁스에서는 CI 도구로 GitLab을, CD 도구로 Jenkins를 사용하고 있다.

### GitLab

GitLab은 git 기반으로 소프트웨어 개발 및 협업을 위한 올인원 솔루션을 제공하는 웹 기반 플랫폼이다.

우리가 개발하는 소스코드들은 git을 사용하여 해당 GitLab의 리포지토리로 자동 통합 시킬 수 있다.

### Jenkins

Jenkins 또한 CI/CD 도구이다. 하지만 뎁스에서는 CD 도구로만 사용하고 있다.

Jenkins는 배포 프로세스를 자동화 할 수 있게 구성이 이루어져 있다. 이를 GitLab과 연동시켜, GitLab에서 Push Event가 발생할 경우에 해당 배포 프로세스가 자동 실행되고, 해당 배포 프로세스가 실패하면 더 이상 프로세스를 진행하지 않고 자동 종료 시킬 수 있는 기능 또한 제공되고 있다.

Jenkins에 대한 자세한 내용은 화면을 살펴보자

[http://10.10.10.200:8080/](http://10.10.10.200:8080/)

### GitLab과 Jenkins의 연결

GitLab의 Push Event를 설정하여 Jenkins와 연결 시키려면 우선 GitLab 관리자 권한이 존재하여야 한다.

Settings > Webhooks

![[web_hook.png]]
해당 내용은 dev 브랜치에 Push Event 발생 시 Jenkins의 cca-front 자동배포를 실행시킨다.
![[jenkins_buildstep.png]]
자동 배포가 실행이 되면 설정한 순서대로 명령어 실행이 이루어진다.