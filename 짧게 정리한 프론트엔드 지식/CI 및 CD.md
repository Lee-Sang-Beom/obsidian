### 1. CI/CD란?

- CI/CD(Continuous Integration/Continuous Deployment or Continuous Delivery)는 소프트웨어 개발 과정에서 자동화를 통해 코드 변경 사항을 빠르고 안정적으로 배포하는 방법론을 의미한다.

---
### 2. CI(Continuous Integration, 지속적 통합)

- CI는 개발자들이 코드 변경 사항을 주기적으로 중앙 저장소(예: Git)에 병합하고, 자동화된 빌드 및 테스트를 수행하여 코드의 품질을 보장하는 과정이다.

#### ✅ CI의 주요 개념
- **자동화된 빌드**: 코드가 변경될 때마다 빌드가 자동으로 실행됨
- **자동화된 테스트**: 유닛 테스트 및 통합 테스트를 자동으로 수행하여 코드 품질 유지
- **빠른 피드백**: 코드 변경 시 즉시 오류를 감지하여 빠르게 해결 가능

---
### 3. CD(Continuous Deployment/Continuous Delivery, 지속적 배포/전달)

- CD는 CI 이후의 단계로, 변경된 코드가 자동으로 배포되거나(Continuous Deployment) 배포 준비 상태로 유지되는(Continuous Delivery) 과정입니다.

### ✅ CD의 주요 개념
- **Continuous Delivery (지속적 전달)**:
  - 코드 변경 사항이 **자동으로 스테이징 환경까지 배포됨**
  - 운영 환경에 배포할지 여부는 **수동 승인**을 통해 결정
  
- **Continuous Deployment (지속적 배포)**
  - 코드 변경 사항이 **운영 환경까지 자동으로 배포됨**
  - **사람의 개입 없이** 새로운 기능이 고객에게 즉시 제공됨

---
### 4. CI/CD 파이프라인

- CI/CD는 일반적으로 파이프라인 형태로 구성되며, 다음과 같은 단계를 포함한다.
	1. **코드 변경** → 개발자가 코드를 수정하고 Git에 PUSH
	2. **빌드(Build)** → 코드가 자동으로 빌드됨
	3. **테스트(Test)** → 자동화된 테스트 실행
	4. **배포(Deploy)** → 코드가 스테이징 또는 운영 환경으로 배포됨

---
### 5. CI/CD 도구

- CI/CD를 구현하기 위해 다양한 도구가 사용될 수 있다.
	- **CI 도구**: Jenkins, GitHub Actions, GitLab CI/CD, CircleCI, Travis CI 등
	- **CD 도구**: ArgoCD, Spinnaker, Flux, AWS CodeDeploy 등
	- **컨테이너 오케스트레이션**: Docker, Kubernetes(K8s)

---
### 6. CI/CD의 장점

- **빠른 배포**: 새로운 기능을 빠르고 안정적으로 출시 가능
- **높은 품질 유지**: 자동화된 테스트를 통해 코드 안정성 확보
- **문제 조기 발견**: 작은 단위로 자주 배포하여 버그를 빠르게 감지
- **개발자 생산성 향상**: 반복적인 수작업 없이 코드 품질 유지 및 배포 가능

