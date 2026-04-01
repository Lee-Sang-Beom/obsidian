
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
좋아.위 vue코드는 이제 src/pages/resource-management/resource/upload-image/resource-upload-image-page.tsx와 연결해야해.
구현 전, 아래의 [구현 시 파일 규칙]와 [유의사항] 들을 살펴봐줘.  
  
===============================================  

[구현 시 참고할 react 코드]
- src/features/resource-management/resource/list/modals/edit/resource-edit-modal.tsx
- 위 경로는 다른페이지의 콘텐츠 정보 수정에 대한 다이얼로그 UI야. 코드를 보면 현재 구현해야할 페이지의 UI와 아주비슷한 걸 확인할 수 있을거야.
- 해당 컴포넌트에서 사용하는 select나 input, checkbox같은 부분은 src/features/resource-management/_shared/components에 모여있어. 구현 시 해당 resource-edit-modal파일을 먼저 확인하고 필요한 코드를 작성하는 방향을 필수적으로 생각해줘

===============================================  

[유의사항]  
- vue의 AdminBasicCard는 구현할 필요 없음  
- 만약, vue에서 특정 상수가 사용된 부분이 있다면, src/constants/domain 하위의 아래 코드를 살펴봐. 필요한 게 있을거야
 > src/constants/domain/category.ts: 카테고리 상수모음 (CATEGORY_TYPE같은게있음)
 > src/constants/domain/resource.ts: 리소스 상수모음 (ORIGINAL_RESOURCE_SE_CON같은게있음)
 > src/constants/domain/state.ts: 상태 상수모음

- resource.ts내에서 사용되는 RESOURCE_SOURCE_SITE는 제거되었어. 
 > 이는 src/hooks/queries/use-common-query-actions.ts의 useSrcFromListQuery를 사용하여 데이터를 조회하는 로직을 사용해야 해. 아래처럼 응답이 날아와.
{
    "items": [
        {
            "srcFromCode": 1,
            "srcFromDes": "아이클릭아트(유료)-1"
        },
        {
            "srcFromCode": 2,
            "srcFromDes": "게티이미지코리아(유료)-2"
        },
        {
            "srcFromCode": 3,
            "srcFromDes": "AI 변환 이미지 (무료)-3"
        },
        {
            "srcFromCode": 50,
            "srcFromDes": "Pexels(무료)-50"
        },
        {
            "srcFromCode": 51,
            "srcFromDes": "Pixabay(무료)-51"
        },
        {
            "srcFromCode": 52,
            "srcFromDes": "Vecteezy(무료)-52"
        },
        {
            "srcFromCode": 53,
            "srcFromDes": "Stocksnap(무료)-53"
        },
        {
            "srcFromCode": 54,
            "srcFromDes": "flickr(무료)-54"
        },
        {
            "srcFromCode": 55,
            "srcFromDes": "무료(무료)-55"
        },
        {
            "srcFromCode": 56,
            "srcFromDes": "AI 생성 이미지(무료)-56"
        },
        {
            "srcFromCode": 57,
            "srcFromDes": "무료(무료)-57"
        },
        {
            "srcFromCode": 58,
            "srcFromDes": "무료(무료)-58"
        },
        {
            "srcFromCode": 59,
            "srcFromDes": "고객 이미지(무료)-59"
        }
    ]
}

===============================================  

[구현 시 파일 규칙]  
- src/lib/category-parser.ts: vue의 categoryParser코드
- src/components/ui/media-lightbox.tsx: vue의 mediaLightbox 컴포넌트
- src/features/resource-management/resource/upload-image/components: 해당 페이지 내에서 사용할 컴포넌트가 정의될 디렉터리 
- src/features/resource-management/resource/upload-image/hooks/mutations/use-resource-upload-image-mutation-actions.ts: (구현완료) 해당 페이지 내에서 이미지 등록을 위해 사용할 POST 요청 훅
- src/features/resource-management/resource/upload-image/resource-upload-image-constants.ts: 페이지 내에서 사용하기 위한 상수  
- src/features/resource-management/resource/upload-image/resource-upload-image-form-schema.ts: 페이지 내에서 사용하기 위한 폼 스키마  
- src/features/resource-management/resource/upload-image/resource-upload-image-handlers.ts: 페이지 내에서 비즈니스로직을 구현하기 위해 정의되는함수가 모일파일  
- src/features/resource-management/resource/upload-image/resource-upload-image-model.ts: (구현완료) 페이지 내에서 비즈니스로직을 구현하기 위해 정의된 타입 (클린아키텍처 준수)    
- src/service/dto/resource-management/resource/resource-list-dto.ts: (구현완료) 서비스 파일과 가까이 있으면서, 서비스파일에서 호출되는 타입(클린아키텍처 준수)  
- src/types/resource-management/resource/resource-list-types.ts: (구현완료) resource-list-model과 resource-list-dto에서 공통으로 사용하는 타입 (클린아키텍처 준수)  


- src/lib/http/api-client.ts: API 요청 시 공통으로 탈 로직. POST할때참고
- src/components/ui/file-upload-dropzone.tsx: 파일업로드 로직사용 시 참고
```;