
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
좋아.위 vue코드는 이제 src/pages/video/render-video/render-video-page.tsx와 연결해야해.
구현 전, 아래의 [구현 시 파일 규칙]와 [유의사항] 들을 살펴봐줘. 

그리고 구현에 필요한 추가적으로 필요한 정보가 있으면 알려줘.
(ex: vue에서는 어떤 상수를 썼는데 react에서는 이 상수가 어디정의되어있나요? 없으면 만들까요? 등등)  

===============================================  

[구현 시 참고할 react 코드]

- src/pages/resource-management/resource/list/resource-list-page.tsx
 > 위 경로는 현재구현해야 하는 것처럼 검색필터가 있고, 클릭하면 다이얼로그가 오픈되는 페이지야
 > 현재 구현해야 할 페이지와 아예 비슷하진 않지만, 검색필터 구성, 페이지네이션 등등의 작업을 할 때 유용할거야
 > 현재 페이지의 검색필터도 저런식으로 구현하면 돼
 
- src/features/resource-management/resource/list/modals/edit/resource-edit-modal.tsx
 > 오픈된 다이얼로그는 이 경로에 있어. 
 > src/provider/modal-provider.tsx에 내가 오픈할 모달정보를 기재된거처럼 넣고 이런식으로 파일을작성하면돼
 > 다이얼로그가 필요하다면 이러한 방법을 사용하면 돼.

- 상세 페이지 이동이 필요하다면, React에서 장려하는 클린 아키텍처 방법론을 따라 개발해주면 돼 
- 그리고 리소스를 표시해야한다면, vue 코드에서는 common.js에 있는 getContentsFile을 썼거든? 여기 react에서는 src/features/resource-management/original-resource/list/components/columns-original-resource-list.tsx에 있는 코드 중 아래코드처럼 getResourceUrl을 통해 리소스정보에 접근하면돼
  
  <img  
  src={getResourceUrl(row.original.resourceUrl)}  
  alt="thumbnail"  
  className="h-12 w-16 object-cover cursor-pointer"  
  onError={handleImageError}  
  onClick={() => onImageClick(row.original)}  
/>

===============================================  

[유의사항]  
- vue의 AdminBasicCard는 구현할 필요 없음  
- 만약, vue에서 특정 상수가 사용된 부분이 있다면, src/constants/domain 하위의 아래 코드를 살펴봐. 필요한 게 있을거야
 > src/constants/domain/category.ts: 카테고리 상수모음 (CATEGORY_TYPE같은게있음)
 > src/constants/domain/resource.ts: 리소스 상수모음 (ORIGINAL_RESOURCE_SE_CON같은게있음)
 > src/constants/domain/state.ts: 상태 상수모음
 > src/constants/domain/video.ts: 템플릿 관련 상수모음
 > src/constants/domain/user.ts: 유저 정보 상수모음

===============================================  

[구현 시 파일 규칙]  
- src/lib/category-parser.ts: vue의 categoryParser코드  
- src/features/video/render-video/components: 해당 페이지 내에서 사용할 컴포넌트들을 이 디렉터리에 정의해줘.  
- src/features/video/render-video/modals: 해당 페이지 내에서 다이얼로그 컴포넌트가 필요할 때, 이 디렉터리에 정의해줘  
- src/features/video/render-video/hooks/modals: 해당 페이지 내에서 다이얼로그 컴포넌트가 필요할 때, 다이얼로그 open 여부를 관리하는 훅을 여기 정의해줘 
- src/features/video/render-video/hooks/queries/use-render-video-query-actions.ts: (구현완료) 해당 페이지 내에서 사용할 GET 요청  
- src/features/video/render-video/render-video-constants.ts: 페이지 내에서 사용하기 위한 상수가 별도로 필요하다면 여기 정의해줘  
- src/features/video/render-video/render-video-handlers.ts: 페이지 내에서 비즈니스로직을 구현하기 위해 함수 정의가 필요하다면, 여기 정의해줘  
- src/features/video/render-video/render-video-model.ts: (구현완료) 페이지 내에서 비즈니스로직을 구현하기 위해 정의된 타입 (클린아키텍처 준수)  
- src/service/dto/video/render-video-dto.ts: (구현완료) 서비스 파일과 가까이 있으면서, 서비스파일에서 호출되는 타입(클린아키텍처 준수)  
- src/types/video/render-video-types.ts: (구현완료) -model과 -dto에서 공통으로 사용하는 타입 (클린아키텍처 준수)  
- src/service/template-service.ts: (구현완료) 해당 페이지에서 요청할 getRenderTemplateRenderList가 정의됨
  
```;