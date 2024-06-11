
#### 1. 코드하우스 필요내용 정리

1. GN-home 전자결재 살릴 거
	- 기안문서 작성: 신규작성 및 임시저장
	- 결재함: 전체문서, 진행문서, 대기문서, 반려/회수문서, 완료문서, 참조문서

2. 만들어야 하는거
	- 서명관리 


#### 2. GN-home 동작

###### 1. 서식관리함 - 업무분류 관리 - 업무분류관리 추가
 - 프론트 : insEaDocWorkMain API 사용 
 - 백엔드 : **ea_docwork_main**에 JPA 사용하여 insert


###### 2. 기안문서작성 - 문서분류 데이터 불러오기
 - 프론트 : selEaDocfolderMain API 사용 
 - 백엔드 : findByKaptCodeOrderByLvAsc 메소드를 이용하여, **ea_docfolder_main**에서 select


###### 3. 기안문서작성 - 문서분류 데이터 - 서식 상세 데이터 불러오기
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
	- 상세조회는 `/api/eadoc/selEaDocapprovalMainDetail/${docSeq}` 사용
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
	 - `selEaDocapprovalMain` 메소드 내, `eaDocapprovalMainService.selEaDocapprovalMain(eaDocapprovalSearchDto);` 로 Page 인스턴스 생성
	 - 이 때, 불러오는 테이블 대상은 `ea_docapproval_main`
	 - 상세조회는 `selEaDocapprovalMainDetail` 메소드에서 `EaDocapprovalMainDto bySeq = eaDocapprovalMainService.findBySeq(eaDocapprovalMainSeq);` 사용


###### 6. 전체문서

- 프론트
	- API : `/api/eadoc/selEaDocapprovalMain`
	- param : 아래와 같다. (`docStateList`가 빈 문자)
```tsx
1. approvalStateList: null
2. approvalUserSeq: null
3. docStateList: ""
4. docTitle: ""
5. eaDoctypeMainSeq: null
6. eaDocworkMainSeq: null
7. endDate: "2024-06-11 23:59:59"
8. kaptCode: "A10026725"
9. keyword: ""
10. orderBy: "DESC"
11. page: 0
12. searchDateColumn: "submitDt"
13. size: 10
14. sort: "submitDt"
15. startDate: "2024-03-11 00:00:00"
16. writerList: ""
17. writerSeq: null
```

- 백엔드
	- `selEaDocapprovalMain` 메소드 실행
	- `Page<EaDocapprovalMainDto> eaDocapprovalMainDtos = eaDocapprovalMainService.selEaDocapprovalMain(eaDocapprovalSearchDto);`을 이용한 응답값 생성
	- 서비스 구현체에 `public Page<EaDocapprovalMainDto> selEaDocapprovalMain(EaDocapprovalSearchDto eaDocapprovalSearchDto) {...}`와 같은 내용이 있음 (이건 jpa 동적쿼리로 구성됨)
	- **ea_docapproval_main** 테이블 사용


###### 7. 진행문서 (전체문서와 동일)

- 프론트
	- API : `/api/eadoc/selEaDocapprovalMain`
	- param : 아래와 같다. (`docStateList`가 "PROGRESS")
```tsx
1. approvalStateList: null
2. approvalUserSeq: "94"
3. docStateList: "PROGRESS"
4. docTitle: ""
5. eaDoctypeMainSeq: null
6. eaDocworkMainSeq: null
7. endDate: "2024-06-11 23:59:59"
8. kaptCode: "A10026725"
9. keyword: ""
10. orderBy: "DESC"
11. page: 0
12. searchDateColumn: "submitDt"
13. size: 10
14. sort: "submitDt"
15. startDate: "2024-03-11 00:00:00"
16. writerList: ""
17. writerSeq: "94"
```

- 백엔드
	- `selEaDocapprovalMain` 메소드 실행
	- `Page<EaDocapprovalMainDto> eaDocapprovalMainDtos = eaDocapprovalMainService.selEaDocapprovalMain(eaDocapprovalSearchDto);`을 이용한 응답값 생성
	- 서비스 구현체에 `public Page<EaDocapprovalMainDto> selEaDocapprovalMain(EaDocapprovalSearchDto eaDocapprovalSearchDto) {...}`와 같은 내용이 있음 (이건 jpa 동적쿼리로 구성됨)
	- **ea_docapproval_main** 테이블 사용


###### 8. 완료문서 (전체문서, 진행문서와 동일)

- 프론트
	- API : `/api/eadoc/selEaDocapprovalMain`
	- param : 아래와 같다. (`docStateList`가 "FINISH")
```tsx
1. approvalStateList: ""
2. approvalUserSeq: "94"
3. docStateList: "FINISH"
4. docTitle: ""
5. eaDoctypeMainSeq: null
6. eaDocworkMainSeq: null
7. endDate: "2024-06-11 23:59:59"
8. kaptCode: "A10026725"
9. keyword: ""
10. orderBy: "DESC"
11. page: 0
12. referenceUserSeq: null
13. searchDateColumn: "finishDt"
14. size: 10
15. sort: "finishDt"
16. startDate: "2024-03-11 00:00:00"
17. writerList: ""
18. writerSeq: "94"
```

- 백엔드
	- `selEaDocapprovalMain` 메소드 실행
	- `Page<EaDocapprovalMainDto> eaDocapprovalMainDtos = eaDocapprovalMainService.selEaDocapprovalMain(eaDocapprovalSearchDto);`을 이용한 응답값 생성
	- 서비스 구현체에 `public Page<EaDocapprovalMainDto> selEaDocapprovalMain(EaDocapprovalSearchDto eaDocapprovalSearchDto) {...}`와 같은 내용이 있음 (이건 jpa 동적쿼리로 구성됨)
	- **ea_docapproval_main** 테이블 사용


###### 9. 미처리문서 (전체문서, 진행문서, 완료문와 동일)

- 프론트
	- API : `/api/eadoc/selEaDocapprovalMain`
	- param : 아래와 같다. (`docStateList`가 "RETURN, WITHDRAW, CANCEL") 
		- 임시저장은 상신되지 않았으므로, 없다.
```tsx
1. approvalStateList: ""
2. approvalUserSeq: null
3. docStateList: "RETURN, WITHDRAW, CANCEL"
4. docTitle: ""
5. eaDoctypeMainSeq: null
6. eaDocworkMainSeq: null
7. endDate: "2024-06-11 23:59:59"
8. kaptCode: "A10026725"
9. keyword: ""
10. orderBy: "DESC"
11. page: 0
12. referenceUserSeq: null
13. searchDateColumn: "submitDt"
14. size: 10
15. sort: "submitDt"
16. startDate: "2024-03-11 00:00:00"
17. writerList: ""
18. writerSeq: "94"
```

- 백엔드
	- `selEaDocapprovalMain` 메소드 실행
	- `Page<EaDocapprovalMainDto> eaDocapprovalMainDtos = eaDocapprovalMainService.selEaDocapprovalMain(eaDocapprovalSearchDto);`을 이용한 응답값 생성
	- 서비스 구현체에 `public Page<EaDocapprovalMainDto> selEaDocapprovalMain(EaDocapprovalSearchDto eaDocapprovalSearchDto) {...}`와 같은 내용이 있음 (이건 jpa 동적쿼리로 구성됨)
	- **ea_docapproval_main** 테이블 사용


#### 10. 사용 API 및 테이블 정리

1. 서식관리함 - 업무분류관리
	- 프론트 
		- 업무분류 리스트 조회 : **selEaDocWorkMain API**
		- 업무분류 추가 : **insEaDocWorkMain API**
		  
	- 백엔드 
		- 업무분류 리스트 조회 : **ea_docwork_main** 테이블에서 **SELECT**
		- 업무분류 추가 : **ea_docwork_main** 테이블에 **INSERT**

2. 서식관리함 - 문서유형관리
	- 프론트 
		- 문서유형 리스트 조회 : **selEaDoctypeMain API**
		- 문서분류 추가 : **insEaDoctypeMain API**
		  
	- 백엔드 
		- 문서분류 리스트 조회 : **ea_doctype_main** 테이블에서 **SELECT**
		- 문서분류 추가 : **ea_doctype_main** 테이블에 **INSERT

3. 기안문서작성 - 문서분류 데이터 불러오기
	 - 프론트 : **selEaDocfolderMain API**
	 - 백엔드 : **ea_docfolder_main** 테이블에서 **SELECT**

4. 기안문서작성 - 공용/단지서식 - 서식 상세 데이터 불러오기
	 - 프론트 : **selEaDocMainDetail API**
	 - 백엔드 : **ea_doc_main** 테이블에서 **JOIN 포함 SELECT**

5. 기안문서작성 -  공용/단지서식 - 서식 상세 데이터 - 임시저장
	 - 프론트 (`docStateEnu="TEMP_SAVE"`)
		 - 저장: **insEaDocapprovalMain API**
		 - 업데이트: **udtEaDocapprovalMain API**
	
	 - 백엔드
		 - 저장 : `insEaDocapprovalMain()`사용: **ea_docapproval_main** 
		 - 수정 : `udtEaDocapprovalMain()`사용: **ea_docapproval_main** 

6. 기안문서작성 -  공용/단지서식 - 서식 상세 데이터 - 상신
	 - 프론트 (`docStateEnu="PROGRESS"`)
		 - 저장: **insEaDocapprovalMain API**
		 - 업데이트: **udtEaDocapprovalMain API**
	
	 - 백엔드
		 - 저장 : `insEaDocapprovalMain()`사용: **ea_docapproval_main** 
		 - 수정 : `udtEaDocapprovalMain()`사용: **ea_docapproval_main** 

7. 임시저장 문서 조회
	 - 프론트(`docStateList=TEMP_SAVE`) 
		 - 리스트 조회 : **selEaDocapprovalMain API**
		 - 상세 조회 : **selEaDocapprovalMainDetail API**
	
	 - 백엔드 : **ea_docapproval_main** 

8. 전체문서 리스트 조회
	- 프론트(`docStateList=""`) : **selEaDocapprovalMain API**
	- 백엔드 : `selEaDocapprovalMain()` 사용 : **ea_docapproval_main**

9. 진행문서 리스트 조회
	- 프론트(`docStateList="PROGRESS"`) : **selEaDocapprovalMain API**
	- 백엔드 : `selEaDocapprovalMain()` 사용 : **ea_docapproval_main**

10. 완료문서 리스트 조회
	- 프론트(`docStateList="FINISH"`) : **selEaDocapprovalMain API**
	- 백엔드 : `selEaDocapprovalMain()` 사용 : **ea_docapproval_main**

11. 미처리문서 리스트 조회
	- 프론트(`docStateList="RETURN, WITHDRAW, CANCEL")` : **selEaDocapprovalMain API**
	- 백엔드 : `selEaDocapprovalMain()` 사용 : **ea_docapproval_main**