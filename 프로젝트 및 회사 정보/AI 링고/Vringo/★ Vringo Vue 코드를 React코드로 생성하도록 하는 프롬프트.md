
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
좋아.위 vue코드는 이제 src/pages/video/upload-template/video-upload-template-page.tsx와 연결해야해.
구현 전, 아래의 [구현 시 파일 규칙]와 [유의사항] 들을 살펴봐줘.  
  
===============================================  

[구현 시 참고할 react 코드]
- src/pages/resource-management/resource/upload-image/resource-upload-image-page.tsx
 > 위 경로는 현재구현해야 하는 것처럼 파일을 업로드하는 페이지야.
 > 파일 업로드 시 src/components/assets/file-upload-dropzone.tsx를 사용할 수 있으면 사용해줘. 
 
===============================================  

[유의사항]  
- vue의 AdminBasicCard는 구현할 필요 없음  
- 만약, vue에서 특정 상수가 사용된 부분이 있다면, src/constants/domain 하위의 아래 코드를 살펴봐. 필요한 게 있을거야
 > src/constants/domain/category.ts: 카테고리 상수모음 (CATEGORY_TYPE같은게있음)
 > src/constants/domain/resource.ts: 리소스 상수모음 (ORIGINAL_RESOURCE_SE_CON같은게있음)
 > src/constants/domain/state.ts: 상태 상수모음

- 파일 업로드 시 src/components/assets/file-upload-dropzone.tsx를 사용할 수 있으면 사용해줘. 파일등록은 여러개되면 안돼
- 등록 후, 페이지가 리다이엑트 되어야한다면 /video/template로 이동해줘 (만약 vue코드에서 리다이엑트기능이없으면 구현하면 안돼.)

===============================================  

[구현 시 파일 규칙]  
- src/lib/category-parser.ts: vue의 categoryParser코드
- src/features/video/upload-template/components: 해당 페이지 내에서 사용할 컴포넌트가 정의될 디렉터리 
- src/features/video/upload-template/hooks/mutations/use-video-upload-template-mutation-actions.ts: (구현완료) 해당 페이지 내에서 파일 등록을 위해 사용할 POST 요청 훅
- src/features/video/upload-template/upload-template-constants.ts: 페이지 내에서 사용하기 위한 상수  
- src/features/video/upload-template/upload-template-handlers.ts: 페이지 내에서 비즈니스로직을 구현하기 위해 정의되는함수가 모일파일  
- src/features/video/upload-template/upload-template-model.ts: (구현완료) 페이지 내에서 비즈니스로직을 구현하기 위해 정의된 타입 (클린아키텍처 준수)    
- src/service/dto/video/upload-template-dto.ts: (구현완료) 서비스 파일과 가까이 있으면서, 서비스파일에서 호출되는 타입(클린아키텍처 준수)  
- src/types/video/upload-template-types.ts: (구현완료) -model과 -dto에서 공통으로 사용하는 타입 (클린아키텍처 준수)  
  
```;