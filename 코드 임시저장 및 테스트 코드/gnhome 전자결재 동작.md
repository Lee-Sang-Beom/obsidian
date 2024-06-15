
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
		- 문서유형 추가 : **insEaDoctypeMain API**
		  
	- 백엔드 
		- 문서유형 리스트 조회 : **ea_doctype_main** 테이블에서 **SELECT**
		- 문서유형 추가 : **ea_doctype_main** 테이블에 **INSERT

3. 서식관리함 - 서식관리 - 서식관리 디렉터리 리스트 불러오기 및 서식 추가, 수정
	- 프론트 
		- 서식관리 디렉터리 리스트 불러오기 : **selEaDocfolderMain API**
		- 추가 : **insEaDocMain API**
		- 수정 : **udtEaDocMain API**
	
	-  백엔드
		- 서식관리 디렉터리 리스트 불러오기 : **ea_docfolder_main** 테이블에서 **SELECT**
		- 추가 : **ea_doc_main** 테이블에 **INSERT** (`insEaDocMain`)
		- 수정 : **ea_doc_main** 테이블에 **UPDATE** (`udtEaDocMain`)

4. 기안문서작성 - 공용서식 or 단지서식 - 문서분류 데이터 불러오기 (공용서식, 단지서식 전자결재문서 `oo관리` **리스트**)
	 - 프론트 : **selEaDocfolderMain API** 
		 - `/api/eadoc/selEaDocfolderMain?kaptCode=${kaptCode}&parentSeq`에서, 공용서식은 kaptCode = -1이며, 단지서식은 kaptCode가 유저의 kaptCode이다.
		 
	 - 백엔드 : **ea_docfolder_main** 테이블에서 **SELECT**

5. 기안문서작성 - 공용서식 or 단지서식 - 특정 oo관리 및 기타 폴더 내에 어떤 데이터가 있는 지 불러오기
	 - 프론트 : **selEaDocMain API** 
		 - `/api/eadoc/selEaDocMain?eaDocfolderMainSeq={}`
		 
	 - 백엔드 : **ea_doc_main** 테이블에서 **SELECT**

6. 기안문서작성 - 공용/단지서식 - 서식 상세 데이터 불러오기
	 - 프론트 : **selEaDocMainDetail API**
	 - 백엔드 : **ea_doc_main** 테이블에서 **JOIN 포함 SELECT**

7. 기안문서작성 -  공용/단지서식 - 서식 상세 데이터 - 임시저장
	 - 프론트 (`docStateEnu="TEMP_SAVE"`)
		 - 저장: **insEaDocapprovalMain API**
		 - 업데이트: **udtEaDocapprovalMain API**
	
	 - 백엔드 (서식에 대해 상세하게 데이터를 작성하고 저장하는 게 ea_docapproval_main에 저장됨)
		 - 저장 : `insEaDocapprovalMain()`사용: **ea_docapproval_main** 
		 - 수정 : `udtEaDocapprovalMain()`사용: **ea_docapproval_main** 

8. 기안문서작성 -  공용/단지서식 - 서식 상세 데이터 - 상신
	 - 프론트 (`docStateEnu="PROGRESS"`)
		 - 저장: **insEaDocapprovalMain API**
		 - 업데이트: **udtEaDocapprovalMain API**
	
	 - 백엔드 (서식에 대해 상세하게 데이터를 작성하고 저장하는 게 ea_docapproval_main에 저장됨)
		 - 저장 : `insEaDocapprovalMain()`사용: **ea_docapproval_main** 
		 - 수정 : `udtEaDocapprovalMain()`사용: **ea_docapproval_main** 

9. 기안문서작성 - 임시저장 문서 조회
	 - 프론트(`docStateList=TEMP_SAVE`) 
		 - 리스트 조회 : **selEaDocapprovalMain API**
		 - 상세 조회 : **selEaDocapprovalMainDetail API**
	
	 - 백엔드 : **ea_docapproval_main** 

10. 전자문서 관리함 - 전체문서 리스트 조회
	- 프론트(`docStateList=""`) : **selEaDocapprovalMain API**
	- 백엔드 : `selEaDocapprovalMain()` 사용 : **ea_docapproval_main**

11. 전자문서 관리함 - 진행문서 리스트 조회
	- 프론트(`docStateList="PROGRESS"`) : **selEaDocapprovalMain API**
	- 백엔드 : `selEaDocapprovalMain()` 사용 : **ea_docapproval_main**

12. 전자문서 관리함 - 완료문서 리스트 조회
	- 프론트(`docStateList="FINISH"`) : **selEaDocapprovalMain API**
	- 백엔드 : `selEaDocapprovalMain()` 사용 : **ea_docapproval_main**

13. 전자문서 관리함 - 미처리문서 리스트 조회
	- 프론트(`docStateList="RETURN, WITHDRAW, CANCEL")` : **selEaDocapprovalMain API**
	- 백엔드 : `selEaDocapprovalMain()` 사용 : **ea_docapproval_main**