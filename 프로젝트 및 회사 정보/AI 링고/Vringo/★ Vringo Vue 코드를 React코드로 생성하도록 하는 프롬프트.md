```
다음의 Vue 코드가 있어.
이는 src/pages/stats/contents/stats-contents-page.tsx와 연결되어있어. 아래 vue코드를 react코드로 구현하는 것이 목표야.



===============================================

[구현 시 주의사항]

1. 타입 및 api 호출을 위한 파일은 아래에 생성되어있어. 이건 다른 페이지도 동일한 구조니까 참고 꼭 해.
- src/features/stats/contents/stats-contents-model.ts: 프론트엔드 내부에서만 사용하는 타입(이미 구현됨. 타입이름은 달라도 내용은 구현되어있어)
- src/service/dto/stats-contents-dto.ts: service 파일에서 백엔드 통신과만 직접적으로 사용하는 타입(이미 구현됨. 타입이름은 달라도 내용은 구현되어있어)
- src/types/stats-common.ts: 위 2개타입파일에서 공통적으로 참조해야하는 요소가 위치한 곳 
   
- src/service/stats-service.ts: 백엔드 통신을 위한 코드  
  
- src/features/stats/contents/hooks/queries/use-stats-contents-query-actions.ts: 백엔드 통신을 위해 구현된 서비스파일을 호출하는 훅  
- src/features/stats/stats-contents-constants.ts: 해당 페이지에서만 사용하는 상수값
- src/features/stats/stats-contents-handler.ts: 해당 페이지에서만 사용하는 유틸성격 함수

  
  
2. 구현 시 참고할만한 것  
- 이미 비슷하게 구현된 코드가 있다.
   > src/pages/settings/video-detail/settings-video-detail-page.tsx나 settings-video-status-page.tsx 와 같은 페이지와 연결된 코드파일들을 참고할 수 있다.  
   > 검색필터의 경우에도 해당 코드를 참고하면 될 거야. (해당 페이지에서는 검색필터의 선택일자가 1개만선택가능하며 월단위로 선택가능하기에 src/components/filters/date-filter.ts를 사용하면되고 granularity를 month로 지정하면된다.)
   > 검색의 경우 꼭, src/pages/settings/contents/settings-contents-page.tsx와 같이 search-filter를 사용한 방법으로 다른페이지 스타일처럼 구현해줘
- useCategory의 경우 이미 있는 파일(src/lib/category-parser.ts)이 있어.
- category.js 관련내용이 필요하다면, src/data/category-constants.ts를 확인해
- 차트는 현재 shadcn-ui의 chart 컴포넌트(src/components/ui/chart)를 사용하면 돼.
- 페이지 구현은 꼭 다른 페이지 경로에 있는 코드들을(예시: src/pages/settings/video-status/settings-video-status-page.tsx) 보고 참고하여 개발해.
- vue의 AdminBasicCard는 구현할 필요는 없음 (이미 다른 react코드는 없음.)
- loadingcomp는 src/components/loader 하위에 있어
```

