

1. 서비스 등록
```json
{
    "srvcNm": "유해화학물질 주요운송경로",
    "srvcExpln": "사고대응 하위 유해화학물질 주요운송경로 메뉴 추가",
    "srcvPath": "/Transport"
}
```

2. 메뉴 추가
```json

{
    "upMenuSeq": 24,
    "level":2,
    "menuNm" : "유해화학물질 주요운송경로",
    "originMenuUrl": "/Transport",
    "menuUrl": "/Transport",
    "nameSort": "홈 > 사고대응 > 유해화학물질 주요운송경로",
    "menuTypeEnu": "PORTAL",
    "mainDisplayYn": "N",
    "displayYn": "N",
    "useYn": "Y",
    "hasChild": "N",
    "sortNo": 6
}
```

3. 갖가지 db변경
- com_srvc : 서비스 변경
- com_menu : 메뉴트리를 위한 베이스
- menu_tree  : 메뉴트리 뷰
- com_auth_menu : 권한별 메뉴 (authrt_menu_seq) : 여기서 use_yn을 yyyn으로 바꿔줘야 함
- com_auth : 권한뭐있는지 확인
