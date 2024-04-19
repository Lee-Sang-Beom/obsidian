> 이미지 출처 및 참고자료: [나른한 개발자님의 포스트 - 테이블 이해하기](https://velog.io/@newdana01/Database-%ED%85%8C%EC%9D%B4%EB%B8%94-%EC%A1%B0%EC%9D%B8-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)

#### 1. JOIN이란?

- 테이블 조인이란 복수의 테이블을 결합하는 것으로, 데이터 조회 시 다른 테이블의 데이터를 함께 조회해야할 때 이용한다.
	- 조인은 크게 Inner join, Outer join으로 나뉜다.


#### 2. INNER JOIN

- JOIN에 대해 얘기할 때는, 보통은 INNER JOIN을 지칭한다.
- **교집합**을 말한다고 생각하자
![[INNERJOIN.png]]

- 만약, 아래 이미지처럼 **Table A와 Table B**가 있다고 가정하자.
	- Table A: 상품 관리 테이블 (Column: 상품코드, 상품명)
	- Table B: 상품 재고 관리 테이블 (Column: 상품코드, 재고수량)
![[INNERJOIN이전예제.png]]
- 조인은 테이블을 결합하는 것이고, 내부조인은 교집합이라고 했다. 
- 각 테이블을 살펴보면, **상품코드**라는 컬럼을 공통으로 가지고 있고 이를 결합 조건으로 하여 아래와 같이 쿼리문을 작성할 수 있다.
	- `ON`: JOIN 시, 결합할 조건을 명시하는 부분이다.
```sql
SELECT A.상품코드 상품코드, A.상품명 상품명, B.재고수량 재고수량
	FROM TableA as A 
    	INNER JOIN TableB as B
    	ON A.상품코드 = B.상품코드
```
![[INNERJOIN이후예제.png]]

- 여기서, 만약 WHERE 절을 추가하여, 특정 데이터만 골라내야 한다면 아래와 같이 작성하면 된다.
	- `WHERE`: 데이터 필터링에 대한 조건
```sql
SELECT A.상품코드 상품코드, A.상품명 상품명, B.재고수량 재고수량 
	FROM TableA as A       
    	INNER JOIN TableB as B   
    	ON A.상품코드 = B.상품코드
    WHERE A.상품코드 = 1 
```

- 아래 조건은 헷갈리지 말자.
> 1. ~를 기준으로 테이블 묶어줘~ -> **ON**
> 2. ~한 데이터만 볼래! -> **WHERE**


#### 3. OUTER JOIN의 개념

- OUTER JOIN(외부 조인)은 **공통 데이터 외에도, 어느 한 테이블에 존재하는 데이터도 함께 결합하여** 조회하고자 할 때 사용하는 방법이다.
- OUTER JOIN(외부 조인)은 **LETF OUTER JOIN, RIGHT OUTER JOIN, FULL OUTER JOIN**이 있는데, 데이터가 한 쪽에만 존재할 때 어느 테이블을 기준으로 삼을지에 따라 나뉜다.

- INNER JOIN(내부 조인)은 공통된 컬럼을 기준으로 테이블을 묶기 때문에 순서가 상관이 없었다
	- 그러나, OUTER JOIN(외부 조인)에서는 순서가 중요하다.
	- **어떤 테이블을 먼저 접근하느냐(드라이빙 테이블)** 에 따라 쿼리 성능에 영향을 미치므로 **더 적은 데이터를 추출하는 테이블을 드라이빙 테이블로 삼는 것이 좋다.**


#### 4. LEFT OUTER JOIN (LEFT JOIN)

- 왼쪽 테이블을 기준으로 OUTER JOIN을 수행하는 것이다.

- 아래는 TableA를 기준으로, 상품 코드 기준 **JOIN**을 진행하는 SQL문이다.
```sql
SELECT A.상품코드 상품코드, A.상품명 상품명, B.재고수량 재고수량 
	FROM TableA as A       
    	LEFT OUTER JOIN TableB as B   
    	ON A.상품코드 = B.상품코드
```

> JOIN 이전
![[INNERJOIN이전예제.png]]

> JOIN 이후
![[LEFTJOIN.png]]

- TableA의 상품코드가 2,5인 row를 살펴보자.
	- TableA는 TableB와 2,5번 상품코드 row에서 공통 데이터가 발견되지 않았다.
	- 따라서, 재고수량이 null로 표시되는 것을 볼 수 있다.

- 정리하면, LEFT OUTER JOIN의 결과로, LEFT OUTER JOINANS 명령문 왼쪽에 위치한 테이블(Table A)을 기준으로 조인되었으며, 왼쪽 테이블의 모든 데이터와 오른쪽 테이블의 중복 데이터가 결합되었다.
	- 이 때, 값이 없는 데이터는 null로 표시가 되었다.


#### 5. RIGHT OUTER JOIN (RIGHT JOIN)

- LEFT OUTER JOIN과 반대로, 오른쪽 테이블을 기준으로 JOIN한다.
```sql
SELECT A.상품코드 상품코드, A.상품명 상품명, B.재고수량 재고수량 
	FROM TableA as A       
    	RIGHT OUTER JOIN TableB as B   
    	ON A.상품코드 = B.상품코드
```

> JOIN 이전
![[INNERJOIN이전예제.png]]

> JOIN 이후
![[RIGHTJOIN.png]]
- TableB의 상품코드 1,3,4 row는 TableA에 공통 데이터가 있으니, 상품명 데이터를 정상적으로 잘 불러왔다.
	- 하지만, 상품코드 7,8 row는  

SELECT A.code code,  A.name name, B.count count 
	FROM TABLEA as A       
    	RIGHT OUTER JOIN TABLEB as B   
    	ON A.code = B.code