```
다음의 Vue 코드가 있어.
이는 src/pages/site-mgmt/account/account-page.tsx와 연결되어있어. 
아래 vue코드 내용이 react코드로 빠짐없이 정상이식되어야 해.


===============================================

[구현 시 파일 규칙]
- src/features/site-mgmt/account/components: 해당 계정관리 페이지 내에서 사용할 컴포넌트가 모일 디렉터리
- src/features/site-mgmt/account/hooks/modals: 해당 계정관리 페이지 내에서 사용할 다이얼로그 컴포넌트가 모임
- src/features/site-mgmt/account/hooks/mutations/use-account-mutation-actions.ts: (구현완료) 해당 계정관리 내에서 POST 관련 서비스 함수를 요청하기 위해 사용할 훅
- src/features/site-mgmt/account/hooks/queries/use-account-query-actions.ts: (구현완료) 해당 계정관리 내에서 GET 관련 서비스 함수를 요청하기 위해 사용할 훅
- src/features/site-mgmt/account/hooks/account-query-keys.ts: (구현완료) 쿼리 키 관련
- src/features/site-mgmt/account/account-constants.ts: 계정관리 페이지 내에서 사용하기 위한 상수
- src/features/site-mgmt/account/account-form-schemas.ts: 계정관리 페이지 내에서 사용하기 위한 폼 스키마
- src/features/site-mgmt/account/account-model.ts: (구현완료) 계정관리 페이지 내에서 비즈니스로직을 구현하기 위해 정의된 타입 (클린아키텍처 준수)
- src/service/dto/site-mgmt/account-dto.ts: (구현완료) 서비스 파일과 가까이 있으면서, 서비스파일에서 호출되는 타입(클린아키텍처 준수)
- src/types/site-mgmt/account-types.ts: (구현완료) account-model과 account-dto에서 공통으로 사용하는 타입 (클린아키텍처 준수)
===============================================

[유의사항]
- vue의 AdminBasicCard는 구현할 필요 없음
- STATE_TYPE, STYLE_TYPE, IMAGE_PAID_TYPE같은 정보가 필요하면 src/constants 하위의 파일들을 꼭 찾아볼 것 (모아둠)
```

