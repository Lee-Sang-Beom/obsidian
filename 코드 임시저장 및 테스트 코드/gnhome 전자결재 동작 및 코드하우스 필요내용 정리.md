
#### 1. 코드하우스 필요내용 정리

1. GN-home 전자결재 살릴 거
	- 기안문서 작성: 신규작성 및 임시저장
	- 결재함: 전체문서, 진행문서, 대기문서, 반려/회수문서, 완료문서, 참조문서

2. 만들어야 하는거
	- 서명관리 


#### 2. GN-home 동작

###### 1. 업무분류관리 추가
 - 프론트 : insEaDocWorkMain API 사용 
 - 백엔드 : ea_docwork_main에 JPA 사용하여 insert
###### 2. 서식 문서분류 불러오기
 - 프론트 : selEaDocfolderMain API 사용 
 - 백엔드 : findByKaptCodeOrderByLvAsc 메소드를 이용하여, ea_docfolder_main에서 select

###### 3. 선택서식 작성
 - 프론트 : `selectedDoc.eaDocMainSeq`를 이용