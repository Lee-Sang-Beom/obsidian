
#### 1. Git Repository
- GIT: [Vringo_CMS_RC_Frontend - 홈](http://dev.ijaksnc.co.kr/Vringo/Vringo_CMS_RC_Frontend)
- Document GIT: [Vringo_DOCS - 홈](http://dev.ijaksnc.co.kr/Vringo/Vringo_DOCS)
- push할 git주소: [Vringo_ICMS_RCT_Frontend - 홈](http://dev.ijaksnc.co.kr/Vringo/Vringo_ICMS_RCT_Frontend)
	- 제작 쪽이 **ICMS**

#### 2. jenkins 주소 및 계정 정보
- jenkins 주소: [Dashboard [Jenkins]](http://192.168.1.225:9100/)
```
[id / password]
admin / dkdlwkr
```

#### 3. Swagger-UI
- Swagger-UI: [Swagger UI](http://192.168.1.90:9000/webjars/swagger-ui/index.html?configUrl=/v3/api-docs/swagger-config)

#### 4. 상용환경 접속 정보
- 주소: [http://222.111.207.110:5013/](http://222.111.207.110:5013/)
```
[id / password]
[admin@admin.com](mailto:admin@admin.com) / 아이작12!@
```

#### 5. 개발환경 접속 및 부팅 정보
- 원격 데스크톱 연결
	- 192.168.1.90:3389
```
[id / password]
admin/dkdlwkr
```

```
이제 API 엔드포인트를 줄테니 너는 dto, service 함수, types, model 파일을 만들어주면 돼.
service함수는 기존 서비스 파일에 함수를 추가하는 방식으로 확장하면 될거야 

2개 API 엔드포인트를 줄건데 타입이름은 각각의 공통이름인 1번째 api RenderTemplateContentsIdList, 2번째 api RenderTemplateProductionList가 되게해주고
이전 예시코드에서 사용했던 접미사 Dto, Query와 같은 공통규칙은 꼭 지켜야해

1. http://192.168.1.90:9000/api-r/template/contents-list-info/1/1?pageSize=50 (RenderTemplateContentsIdList)
- 요청은 id, nowPage, pageSize로 모두 number야.
 > getTemplateInfoList와 비슷하다고 볼 수 있을 것 같아.

- 응답은 아래와 같아.
 > 응답 데이터 형태와 실제 데이터를 줄게
{
description:	
음답 ArrayList 객체

id*	integer($int64)
example: 1
ID

createdAt*	string($date-time)
등록일

renderType*	integer($int32)
구분(자동:0,수동:1)

templateName*	string
파일명

imageCount*	integer($int32)
이미지

username*	string
가공자

tdiff*	string
렌더링 시간

active*	integer($int32)
등록(0:미등록,1:등록)

thumbnailURL*	string
섬네일 경로

previewURL*	string
영상 경로

}

{
    "nowPage": 1,
    "pageSize": 40,
    "totalCount": 17772,
    "items": [
        {
            "id": 102404,
            "createdAt": "2026-04-16T15:15:16",
            "renderType": 1,
            "templateName": "m_0099_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102403,
            "createdAt": "2026-04-16T15:10:30",
            "renderType": 1,
            "templateName": "m_0086_공통_공통_일반_다이나믹",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102402,
            "createdAt": "2026-04-16T15:02:14",
            "renderType": 1,
            "templateName": "m_0105_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102401,
            "createdAt": "2026-04-16T15:01:55",
            "renderType": 1,
            "templateName": "m_0105_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102400,
            "createdAt": "2026-04-16T14:36:19",
            "renderType": 1,
            "templateName": "m_0111_공통_공통_일반_타이포",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102399,
            "createdAt": "2026-04-16T14:25:02",
            "renderType": 1,
            "templateName": "m_0084_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102398,
            "createdAt": "2026-04-16T14:20:10",
            "renderType": 1,
            "templateName": "m_0103_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 2,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102397,
            "createdAt": "2026-04-16T14:08:54",
            "renderType": 1,
            "templateName": "m_0037_공통_공통_일반_컬러풀",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102396,
            "createdAt": "2026-04-16T13:53:09",
            "renderType": 1,
            "templateName": "m_0099_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102395,
            "createdAt": "2026-04-16T13:47:43",
            "renderType": 1,
            "templateName": "m_0084_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102394,
            "createdAt": "2026-04-16T13:39:50",
            "renderType": 1,
            "templateName": "m_0084_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102393,
            "createdAt": "2026-04-15T16:28:41",
            "renderType": 1,
            "templateName": "m_0084_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102392,
            "createdAt": "2026-04-15T16:23:29",
            "renderType": 1,
            "templateName": "m_0077_공통_공통_일반_컬러풀",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102391,
            "createdAt": "2026-04-15T16:18:49",
            "renderType": 1,
            "templateName": "m_0099_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102390,
            "createdAt": "2026-04-15T16:16:30",
            "renderType": 1,
            "templateName": "m_0105_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102389,
            "createdAt": "2026-04-15T16:12:11",
            "renderType": 1,
            "templateName": "m_0062_공통_공통_일반_타이포",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102388,
            "createdAt": "2026-04-15T16:02:41",
            "renderType": 1,
            "templateName": "m_0084_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102387,
            "createdAt": "2026-04-15T15:53:34",
            "renderType": 1,
            "templateName": "m_0084_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102386,
            "createdAt": "2026-04-15T15:43:29",
            "renderType": 1,
            "templateName": "m_0105_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102385,
            "createdAt": "2026-04-15T15:36:38",
            "renderType": 1,
            "templateName": "m_0082_공통_공통_일반_컬러풀",
            "renderCount": 1,
            "renderSuccessCount": 2,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102384,
            "createdAt": "2026-04-15T15:27:35",
            "renderType": 1,
            "templateName": "m_0031_공통_공통_일반_컬러풀",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102383,
            "createdAt": "2026-04-15T15:21:28",
            "renderType": 1,
            "templateName": "m_0099_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102382,
            "createdAt": "2026-04-15T15:12:55",
            "renderType": 1,
            "templateName": "m_0074_공통_공통_일반_전단지",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102381,
            "createdAt": "2026-04-15T15:04:20",
            "renderType": 1,
            "templateName": "m_0095_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102380,
            "createdAt": "2026-04-15T14:57:15",
            "renderType": 1,
            "templateName": "m_0067_공통_공통_일반_다이나믹",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102379,
            "createdAt": "2026-04-15T14:49:21",
            "renderType": 1,
            "templateName": "m_0079_공통_공통_일반_타이포",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102378,
            "createdAt": "2026-04-15T14:42:33",
            "renderType": 1,
            "templateName": "m_0004_공통_공통_일반_다이나믹",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102377,
            "createdAt": "2026-04-15T14:34:50",
            "renderType": 1,
            "templateName": "m_0097_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102376,
            "createdAt": "2026-04-15T14:31:40",
            "renderType": 1,
            "templateName": "m_0108_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102375,
            "createdAt": "2026-04-15T14:29:20",
            "renderType": 1,
            "templateName": "m_0105_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102374,
            "createdAt": "2026-04-14T16:38:11",
            "renderType": 1,
            "templateName": "m_0097_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102373,
            "createdAt": "2026-04-14T16:29:13",
            "renderType": 1,
            "templateName": "m_0045_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102372,
            "createdAt": "2026-04-14T16:13:54",
            "renderType": 1,
            "templateName": "m_0061_공통_공통_일반_전단지",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102371,
            "createdAt": "2026-04-14T16:03:36",
            "renderType": 1,
            "templateName": "m_0096_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102370,
            "createdAt": "2026-04-14T15:57:48",
            "renderType": 1,
            "templateName": "m_0105_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102369,
            "createdAt": "2026-04-14T15:52:47",
            "renderType": 1,
            "templateName": "m_0045_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102368,
            "createdAt": "2026-04-14T15:44:38",
            "renderType": 1,
            "templateName": "m_0062_공통_공통_일반_타이포",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102367,
            "createdAt": "2026-04-14T15:35:51",
            "renderType": 1,
            "templateName": "m_0084_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102366,
            "createdAt": "2026-04-14T15:25:27",
            "renderType": 1,
            "templateName": "m_0084_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        },
        {
            "id": 102365,
            "createdAt": "2026-04-14T15:18:25",
            "renderType": 1,
            "templateName": "m_0095_공통_공통_일반_심플",
            "renderCount": 1,
            "renderSuccessCount": 1,
            "renderActiveCount": 0,
            "username": "user1"
        }
    ]
}




===




2. http://192.168.1.90:9000/api-r/template/template-production-list/1?tplStyleCode=1&cateId=10101&pageSize=100 (RenderTemplateProductionList)
- 요청은 nowPage, pageSize, tplStyleCode, cateId
| tplStyleCode*                      | integer($int32) |
|------------------------------------|-----------------|
| example: 1                         |
| 스타일 코드                             |
|                                    |
| cateId*                            | integer($int32) |
| example: 10101                     |
| 카테고리(0:전체,1자리(업태),3자리(업종),5자리(옵션)) |
|                                    |
| pageSize*                          | integer($int32) |
| example: 100                       |
| 페이지 사이즈                            |

 > 마찬가지로 getTemplateInfoList와 비슷하다고 볼 수 있을 것 같아.

- 응답은 아래와 같아. 마찬가지로 명세와 예시데이터를 줄게
  {

|   |   |
|---|---|
|description:|음답 ArrayList 객체|
|id*|integer($int64)  <br>example: 1<br><br>ID|
|templateId*|string  <br>example: i_0001<br><br>템플릿아이디|
|templateStyle*|integer($int32)  <br>example: 1<br><br>스타일|
|cateId1*|integer($int32)<br><br>대분류|
|cateId2*|integer($int32)<br><br>중분류|
|cateId3*|integer($int32)<br><br>소분류|
|thumbnailURL*|string<br><br>섬네일 url|
|previewURL*|string<br><br>영상 url|

}


{
    "nowPage": 1,
    "pageSize": 12,
    "totalCount": 43,
    "items": [
        {
            "id": 5062,
            "templateId": "m_0153",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 110,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/62/m_0153_thumbnail.png",
            "previewURL": "/mp4/3/62/m_0153_result.mp4"
        },
        {
            "id": 5061,
            "templateId": "m_0152",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 110,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/61/m_0152_thumbnail.png",
            "previewURL": "/mp4/3/61/m_0152_result.mp4"
        },
        {
            "id": 5060,
            "templateId": "m_0151",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 103,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/60/m_0151_thumbnail.png",
            "previewURL": "/mp4/3/60/m_0151_result.mp4"
        },
        {
            "id": 5059,
            "templateId": "m_0150",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 103,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/59/m_0150_thumbnail.png",
            "previewURL": "/mp4/3/59/m_0150_result.mp4"
        },
        {
            "id": 5045,
            "templateId": "m_0144",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 104,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/45/m_0144_thumbnail.png",
            "previewURL": "/mp4/3/45/m_0144_result.mp4"
        },
        {
            "id": 5044,
            "templateId": "m_0142",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 103,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/44/m_0142_thumbnail.png",
            "previewURL": "/mp4/3/44/m_0142_result.mp4"
        },
        {
            "id": 5043,
            "templateId": "m_0143",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 103,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/43/m_0143_thumbnail.png",
            "previewURL": "/mp4/3/43/m_0143_result.mp4"
        },
        {
            "id": 5042,
            "templateId": "m_0141",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 103,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/42/m_0141_thumbnail.png",
            "previewURL": "/mp4/3/42/m_0141_result.mp4"
        },
        {
            "id": 5039,
            "templateId": "m_0139",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 101,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/39/m_0139_thumbnail.png",
            "previewURL": "/mp4/3/39/m_0139_result.mp4"
        },
        {
            "id": 5037,
            "templateId": "m_0138",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 101,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/37/m_0138_thumbnail.png",
            "previewURL": "/mp4/3/37/m_0138_result.mp4"
        },
        {
            "id": 5016,
            "templateId": "m_0127",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 103,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/16/m_0127_thumbnail.png",
            "previewURL": "/mp4/3/16/m_0127_result.mp4"
        },
        {
            "id": 5012,
            "templateId": "m_0124",
            "templateStyle": 1,
            "cateId1": 1,
            "cateId2": 103,
            "cateId3": 1,
            "thumbnailURL": "/mp4/3/12/m_0124_thumbnail.png",
            "previewURL": "/mp4/3/12/m_0124_result.mp4"
        }
    ]
}


```