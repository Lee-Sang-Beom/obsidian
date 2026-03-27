
- 1단계
```
작업요청을 크게 3개단계로 요청할거야.

1. 1번째는 vue코드를 주면 그 코드를 먼저 분석하는 단계. 분석만 하는단계야.
2. 2번째는 해당 vue코드를 구현하기 위해 참고하고, 어떻게 react로 구현하면 될지 예시 react코드를 전달할거야. 
   - 이걸보고 해당방식대로 구현을 시작하기 전 네가 react코드를 분석하고 어떻게 구현할지 마지막으로 나에게 분석결과를 알려줘. 
   - 예시는 다음과 같아
      > ??와 같이 분석했고 ??방식대로 구현하겠습니다. 
      > 구현을 위해 필요한게 있다면 구현 이전, ??이 있는지 확인이 필요합니다.

3. 이제 2번째과정에서 구현을 시작하라는 응답을 받으면 그때부터 구현을 시작하면돼
   
이제 1단계에 해당하는 vue코드를 줄게. 분석만 해 줄 수 있을까?
```

- 2단계
```
다음의 Vue 코드가 있어.  
'분석'만 진행하면 돼. 코드를 직접적으로 바꾸거나 생성할 필요가 없어.


```

- 3단계
```  
좋아.위 vue코드는 이제 src/pages/original-resource/list/original-resource-page.tsx와 연결해야해.
지금 올려주는 react코드를 보기 전에 아래의 [구현 시 파일 규칙]와 [유의사항] 들을 살펴봐줘.  
  
[구현 시 파일 규칙]  

- src/lib/category-parser.ts: vue의 categoryParser코드
- src/features/original-resource/list/components/...: 해당 원천 콘텐츠 관리 > 콘텐츠 목록 페이지 내에서 사용할 컴포넌트가 모일 디렉터리  
- src/components/ui/media-lightbox.tsx: vue의 mediaLightbox 컴포넌트
- src/features/original-resource/list/hooks/modals/...: 해당 원천 콘텐츠 관리 > 콘텐츠 목록 페이지 내에서 사용할 다이얼로그 컴포넌트가 모임  
- src/features/original-resource/list/hooks/mutations/use-original-resource-mutation-actions.ts: (구현완료. 하지만 현재 페이지에는 POST요청이 없어서 구현할 필요 없음) 해당 원천 콘텐츠 관리 > 콘텐츠 목록 내에서 POST 관련 서비스 함수를 요청하기 위해 사용할 훅  
- src/features/original-resource/list/original-resource-handlers.ts: 페이지 구현을 위해 필요한 함수(핸들러)의 모임
- src/features/original-resource/list/hooks/queries/use-original-resource-query-actions.ts: (구현완료) 해당 원천 콘텐츠 관리 > 콘텐츠 목록 내에서 GET 관련 서비스 함수를 요청하기 위해 사용할 훅  
- src/features/original-resource/list/hooks/original-resource-query-keys.ts: (구현완료) 쿼리 키 관련  
- src/features/original-resource/list/original-resource-constants.ts: 원천 콘텐츠 관리 > 콘텐츠 목록 페이지 내에서 사용하기 위한 상수  
- src/features/original-resource/list/original-resource-form-schemas.ts: 원천 콘텐츠 관리 > 콘텐츠 목록 페이지 내에서 사용하기 위한 폼 스키마  
- src/features/original-resource/list/original-resource-model.ts: (구현완료) 원천 콘텐츠 관리 > 콘텐츠 목록 페이지 내에서 비즈니스로직을 구현하기 위해 정의된 타입 (클린아키텍처 준수)  
- src/service/dto/original-resource-dto.ts: (구현완료) 서비스 파일과 가까이 있으면서, 서비스파일에서 호출되는 타입(클린아키텍처 준수)  
- src/types/resource/original-resource-types.ts: (구현완료) original-resource-model과 original-resource-dto에서 공통으로 사용하는 타입 (클린아키텍처 준수)  
  
===============================================  
  
[유의사항]  
- vue의 AdminBasicCard는 구현할 필요 없음   - 만약, vue에서 특정 상수가 사용된 부분이 있다면, src/constants/domain 하위의 아래 코드를 살펴봐. 필요한 게 있을거야
 > src/constants/domain/category.ts: 카테고리 상수모음 (CATEGORY_TYPE같은게있음)
 > src/constants/domain/resource.ts: 리소스 상수모음 (ORIGINAL_RESOURCE_SE_CON같은게있음)
 > src/constants/domain/state.ts: 상태 상수모음

- VringoCategorySelect, VringoSourceSelect등은 vue처럼 직접구현할필요없음. 하위 참고 react코드의 searchfilter를 이용하는방법으로 진행

===============================================  

[구현 시 참고할 react 코드]


```;