
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

###### 3. 선택서식 불러오기
 - 프론트 : `selectedDoc.eaDocMainSeq`를 이용해 selEaDocMainDetail API 호출
 - 백엔드 : EaDocMainRepository.findBySeq로 아래 SELECT문 수행
```sql
@Query("select em from EaDocMain em " +  
        "join fetch em.aptMain ap " +  
        "left join fetch em.eaDocfolderMain ef " +  
        "left join fetch em.eaDoctypeMain et " +  
        "where em.seq = :seq ")
Optional<EaDocMain> findBySeq(@Param("seq") Long seq);
```


###### 4. 공용서식/단지서식 임시저장 및 상신

※ **4-1. 임시저장**

- 프론트 : `docStateEnu`를 아래와 같이 지정하고 API 요청
	- 저장 : insEaDocapprovalMain (결재문서 생성)
	- 업데이트 : udtEaDocapprovalMain (결재문서 수정)
```tsx
copy.docStateEnu = "TEMP_SAVE"
```
- 백엔드 (ea_docapproval_main TABLE에 저장)
	- 저장 : 트랜잭션을 밖에 걸어두고 `public void insEaDocapprovalMain` 메소드 실행 
	- 수정 : 트랜잭션을 밖에 걸어두고 `public void udtEaDocapprovalMain` 메소드 실행


※ **4-2. 상신**

- 프론트 : `docStateEnu`를 아래와 같이 지정하고 API 요청
	- 저장 : insEaDocapprovalMain
	- 업데이트 : udtEaDocapprovalMain
```tsx
copy.docStateEnu = "PROGRESS";
```
- 백엔드 (ea_docapproval_main TABLE에 저장)
	- 저장 : 트랜잭션을 밖에 걸어두고 `public void insEaDocapprovalMain` 메소드 실행 
	- 수정 : 트랜잭션을 밖에 걸어두고 `public void udtEaDocapprovalMain` 메소드 실행


###### 5. 임시저장 문서 조회

- 프론트 : `docStateList`를 `TEMP_SAVE`로 지정한 다음 `/api/eadoc/selEaDocapprovalMain` API 요청 (아래는 적용 params)
```tsx
1. approvalUserSeq: null
2. docStateList: "TEMP_SAVE"
3. docTitle: ""
4. eaDoctypeMainSeq: null
5. eaDocworkMainSeq: null
6. endDate: "2024-06-11 23:59:59"
7. kaptCode: "A10026725"
8. keyword: ""
9. orderBy: "DESC"
10. page: 0
11. searchDateColumn: "regDt"
12. size: 10
13. sort: "regDt"
14. startDate: "2024-03-11 00:00:00"
15. writerList: ""
16. writerSeq: "94"
```

- 백엔드
	 - ``