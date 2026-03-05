```
다음의 Vue 코드가 있어.
이는 src/pages/settings/video/video-status에서 구현해야할 요소야.

===============================================

[구현 시 주의사항]

1. 타입 및 api 호출을 위한 코드는 아래에 정의되어있어.  
- src/features/settings/video-status/settings-video-status-model.ts: 프론트엔드 내부에서만 사용하는 타입  
- src/service/dto/settings-video-status-dto.ts: service 파일에서 백엔드 통신과만 직접적으로 사용하는 타입  
- src/types/settings-video-status-common.ts: 위 2개타입파일에서 공통적으로 참조해야하는 요소가 위치한 곳  
- src/service/settings-video-status-service.ts: 백엔드 통신을 위한 코드  
- src/features/settings/video-status/hooks/mutations/use-settings-video-status-mutation-actions.ts: 백엔드 통신을 위해 구현된 서비스파일을 호출하는 훅  
  
  
2. 구현 시 참고할만한 것  
- 이미 비슷하게 구현된 코드가 있어. src/pages/settings/video-detail/settings-video-detail-page.tsx와 연결된 코드파일들을 보면 될 거야.  
> 검색필터의 경우에도 해당 코드를 참고하면 될 거야.
> useCategory의 경우 이미 있는 파일(src/lib/category-parser.ts)
   
```

