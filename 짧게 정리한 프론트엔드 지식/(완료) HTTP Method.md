## 📌 HTTP Method란?

**HTTP Method**는 클라이언트와 서버가 통신하기 위해 사용하는 메소드입니다.

서버에게 "이 리소스를 어떻게 처리해주세요"라고 요청하는 방식을 정의하는 것으로, **RESTful API**의 핵심 개념입니다.

### 주요 HTTP Method

| Method     | 용도    | 멱등성 | 안전성 | Body |
| ---------- | ----- | --- | --- | ---- |
| **GET**    | 조회    | ✅   | ✅   | ❌    |
| **POST**   | 생성    | ❌   | ❌   | ✅    |
| **PUT**    | 전체 수정 | ✅   | ❌   | ✅    |
| **PATCH**  | 부분 수정 | ❌   | ❌   | ✅    |
| **DELETE** | 삭제    | ✅   | ❌   | ❌    |

---

## 1️⃣ GET - 데이터 조회

### 개념

**GET**은 서버로부터 데이터를 조회하기 위한 용도로 사용합니다.

```http
GET /users/123 HTTP/1.1
Host: api.example.com
```

### 특징

#### ✅ 데이터 전송 방식

**URL에 Query String 형식으로 데이터를 전송**합니다.

```
❌ Body 사용 안 함
✅ URL 사용

예시:
https://api.example.com/search?keyword=nextjs&page=1&limit=10
                              ↑
                         Query String
```

#### 📊 상세 특징

|항목|설명|
|---|---|
|**데이터 위치**|URL (Query String)|
|**Body 사용**|❌ 사용 안 함|
|**길이 제한**|⚠️ URL 최대 길이 고려 필요 (브라우저마다 다름)|
|**보안**|⚠️ URL에 노출되어 취약|
|**캐싱**|✅ 가능 (성능 최적화)|
|**브라우저 기록**|✅ 남음|
|**북마크**|✅ 가능|
|**멱등성**|✅ 있음|
|**안전성**|✅ 서버 상태 변경 안 함|

### 사용 예시

#### 기본 조회

```http
GET /api/users HTTP/1.1
Host: example.com

→ 모든 사용자 목록 조회
```

#### 특정 리소스 조회

```http
GET /api/users/123 HTTP/1.1
Host: example.com

→ ID가 123인 사용자 조회
```

#### Query String 사용

```http
GET /api/products?category=electronics&price_max=50000&sort=latest HTTP/1.1
Host: example.com

→ 필터링 + 정렬된 제품 목록 조회
```

### 코드 예시

#### JavaScript (Fetch API)

```javascript
// 기본 GET 요청
fetch('https://api.example.com/users/123')
  .then(response => response.json())
  .then(data => console.log(data));

// Query String 포함
const params = new URLSearchParams({
  keyword: 'nextjs',
  page: 1,
  limit: 10
});

fetch(`https://api.example.com/search?${params}`)
  .then(response => response.json())
  .then(data => console.log(data));
```

#### Axios

```javascript
// 기본 GET 요청
axios.get('https://api.example.com/users/123')
  .then(response => console.log(response.data));

// Query String (params 옵션)
axios.get('https://api.example.com/search', {
  params: {
    keyword: 'nextjs',
    page: 1,
    limit: 10
  }
}).then(response => console.log(response.data));
```

### 주의사항

#### ⚠️ URL 길이 제한

브라우저마다 URL 최대 길이가 다릅니다:

|브라우저|최대 길이|
|---|---|
|Chrome|~8,192자|
|Firefox|~65,536자|
|IE|~2,083자|
|Safari|~80,000자|

```
❌ 나쁜 예 (긴 데이터)
GET /api/search?data=매우매우매우...긴데이터... (수천 자)

✅ 좋은 예
POST /api/search (데이터는 Body에)
```

#### ⚠️ 보안 취약점

```
❌ 민감 정보를 GET으로 전송하지 마세요!

GET /api/login?username=admin&password=1234
                              ↑
                      URL에 노출 (위험!)

✅ 민감 정보는 POST + Body + HTTPS
```

### 캐싱의 이점

```
첫 번째 요청:
Client → [GET /api/products] → Server
       ← [데이터 + 캐시 헤더] ←

두 번째 요청 (같은 URL):
Client → [캐시 확인] → ⚡ 캐시에서 즉시 응답
           (서버 요청 없음!)

→ 빠른 응답 속도
→ 서버 부하 감소
→ 네트워크 비용 절감
```

---

## 2️⃣ POST - 데이터 생성

### 개념

**POST**는 리소스 데이터를 생성하는 목적으로 사용합니다.

```http
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "name": "홍길동",
  "email": "hong@example.com"
}
```

### 특징

#### ✅ 데이터 전송 방식

**HTTP 메시지의 Body에 데이터를 담아서 전송**합니다.

```
✅ Body 사용
❌ URL에 데이터 노출 안 함

예시:
POST /api/users HTTP/1.1
Content-Type: application/json

{
  "name": "홍길동",
  "age": 30
}
```

#### 📊 상세 특징

|항목|설명|
|---|---|
|**데이터 위치**|HTTP Message Body|
|**Body 사용**|✅ 필수|
|**길이 제한**|✅ 제한 없음 (대용량 가능)|
|**보안**|✅ URL에 노출 안 됨 (HTTPS 권장)|
|**캐싱**|❌ 불가능|
|**브라우저 기록**|❌ 남지 않음|
|**북마크**|❌ 불가능|
|**멱등성**|❌ 없음|
|**안전성**|❌ 서버 상태 변경|

### Content-Type

POST 요청 시 **Content-Type 헤더**에 데이터 타입을 명시해야 합니다.

|Content-Type|용도|예시|
|---|---|---|
|`application/json`|JSON 데이터|API 요청 (가장 일반적)|
|`application/x-www-form-urlencoded`|폼 데이터|HTML 폼 제출|
|`multipart/form-data`|파일 업로드|이미지, 동영상 등|
|`text/plain`|텍스트|단순 텍스트|

### 사용 예시

#### JSON 데이터 전송

```http
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/json
Content-Length: 67

{
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 30
}
```

#### 폼 데이터 전송

```http
POST /api/users HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

name=%ED%99%8D%EA%B8%B8%EB%8F%99&email=hong@example.com&age=30
```

#### 파일 업로드

```http
POST /api/upload HTTP/1.1
Host: example.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary

------WebKitFormBoundary
Content-Disposition: form-data; name="file"; filename="image.jpg"
Content-Type: image/jpeg

[바이너리 데이터]
------WebKitFormBoundary--
```

### 코드 예시

#### JavaScript (Fetch API)

```javascript
// JSON 데이터 전송
fetch('https://api.example.com/users', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    name: '홍길동',
    email: 'hong@example.com',
    age: 30
  })
})
  .then(response => response.json())
  .then(data => console.log(data));

// 파일 업로드
const formData = new FormData();
formData.append('file', fileInput.files[0]);
formData.append('title', '제목');

fetch('https://api.example.com/upload', {
  method: 'POST',
  body: formData // Content-Type 자동 설정
})
  .then(response => response.json())
  .then(data => console.log(data));
```

#### Axios

```javascript
// JSON 데이터 전송
axios.post('https://api.example.com/users', {
  name: '홍길동',
  email: 'hong@example.com',
  age: 30
})
  .then(response => console.log(response.data));

// 파일 업로드
const formData = new FormData();
formData.append('file', fileInput.files[0]);

axios.post('https://api.example.com/upload', formData, {
  headers: {
    'Content-Type': 'multipart/form-data'
  }
})
  .then(response => console.log(response.data));
```

### 대용량 데이터 전송

```
GET의 한계:
URL 길이 제한 → 대량 데이터 전송 불가

POST의 장점:
Body 사용 → 제한 없음 ✅

예시:
- 대용량 JSON 데이터
- 이미지/동영상 파일
- 복잡한 검색 조건
- 대량의 배열 데이터
```

---

## 3️⃣ PUT - 전체 수정

### 개념

**PUT**은 데이터를 추가하거나 기존 데이터 전체를 바꾸는 덮어쓰기 용도로 사용합니다.

```http
PUT /api/users/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 31,
  "address": "서울시 강남구"
}
```

### 특징

|항목|설명|
|---|---|
|**용도**|리소스 전체 교체/생성|
|**멱등성**|✅ 있음 (여러 번 요청해도 결과 동일)|
|**부분 수정**|❌ 불가능 (전체만 가능)|
|**누락 필드**|⚠️ null 또는 기본값으로 변경|

### 동작 방식

```
기존 데이터:
{
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 30,
  "address": "서울시"
}

PUT 요청 (일부 필드만 전송):
{
  "name": "김철수",
  "email": "kim@example.com"
}

결과 (전체 교체):
{
  "name": "김철수",
  "email": "kim@example.com",
  "age": null,        ← ⚠️ 누락된 필드
  "address": null     ← ⚠️ null로 변경됨
}
```

### 사용 예시

#### 사용자 정보 전체 수정

```http
PUT /api/users/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 31,
  "address": "서울시 강남구",
  "phone": "010-1234-5678"
}
```

#### 리소스 생성 (없으면)

```http
PUT /api/users/999 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "name": "이영희",
  "email": "lee@example.com"
}

→ ID 999가 없으면 생성, 있으면 전체 교체
```

### 코드 예시

```javascript
// Fetch API
fetch('https://api.example.com/users/123', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    name: '홍길동',
    email: 'hong@example.com',
    age: 31,
    address: '서울시 강남구'
  })
})
  .then(response => response.json())
  .then(data => console.log(data));

// Axios
axios.put('https://api.example.com/users/123', {
  name: '홍길동',
  email: 'hong@example.com',
  age: 31,
  address: '서울시 강남구'
})
  .then(response => console.log(response.data));
```

---

## 4️⃣ PATCH - 부분 수정

### 개념

**PATCH**는 데이터의 일부만 수정하기 위한 용도로 사용합니다.

```http
PATCH /api/users/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "age": 31
}
```

### 특징

|항목|설명|
|---|---|
|**용도**|리소스 일부만 수정|
|**멱등성**|❌ 없음 (구현에 따라 다름)|
|**부분 수정**|✅ 가능|
|**누락 필드**|✅ 기존 값 유지|

### PUT vs PATCH 비교

```
기존 데이터:
{
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 30,
  "address": "서울시"
}

┌─────────────────────────────────────────┐
│ PUT 요청 (전체 교체)                      │
├─────────────────────────────────────────┤
│ 요청:                                    │
│ { "age": 31 }                           │
│                                         │
│ 결과:                                    │
│ {                                       │
│   "age": 31,                            │
│   "name": null,     ← ⚠️ 삭제됨         │
│   "email": null,    ← ⚠️ 삭제됨         │
│   "address": null   ← ⚠️ 삭제됨         │
│ }                                       │
└─────────────────────────────────────────┘

┌─────────────────────────────────────────┐
│ PATCH 요청 (부분 수정)                    │
├─────────────────────────────────────────┤
│ 요청:                                    │
│ { "age": 31 }                           │
│                                         │
│ 결과:                                    │
│ {                                       │
│   "name": "홍길동",     ← ✅ 유지       │
│   "email": "hong@...",  ← ✅ 유지       │
│   "age": 31,            ← ✅ 수정       │
│   "address": "서울시"   ← ✅ 유지       │
│ }                                       │
└─────────────────────────────────────────┘
```

### 사용 예시

#### 나이만 수정

```http
PATCH /api/users/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "age": 31
}

→ 나이만 31로 변경, 나머지 필드는 그대로 유지
```

#### 여러 필드 수정

```http
PATCH /api/users/123 HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "age": 31,
  "address": "서울시 강남구"
}

→ 나이와 주소만 변경, name과 email은 유지
```

### 코드 예시

```javascript
// Fetch API
fetch('https://api.example.com/users/123', {
  method: 'PATCH',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    age: 31 // 나이만 수정
  })
})
  .then(response => response.json())
  .then(data => console.log(data));

// Axios
axios.patch('https://api.example.com/users/123', {
  age: 31
})
  .then(response => console.log(response.data));
```

### 실전 사용 케이스

```javascript
// ✅ 좋은 예: 상태만 변경
PATCH /api/orders/456
{
  "status": "shipped"
}

// ✅ 좋은 예: 조회수 증가
PATCH /api/posts/789
{
  "views": 101
}

// ❌ 나쁜 예: 모든 필드 전송 (PUT을 쓰세요)
PATCH /api/users/123
{
  "name": "홍길동",
  "email": "hong@example.com",
  "age": 31,
  "address": "서울시",
  "phone": "010-1234-5678"
}
```

---

## 5️⃣ DELETE - 삭제

### 개념

**DELETE**는 특정 리소스의 삭제를 요청하는 데 사용합니다.

```http
DELETE /api/users/123 HTTP/1.1
Host: example.com
```

### 특징

|항목|설명|
|---|---|
|**용도**|리소스 삭제|
|**멱등성**|✅ 있음|
|**Body**|❌ 일반적으로 사용 안 함|
|**응답**|204 No Content 또는 200 OK|

### 사용 예시

#### 단일 리소스 삭제

```http
DELETE /api/users/123 HTTP/1.1
Host: example.com

→ ID가 123인 사용자 삭제
```

#### 응답 예시

```http
HTTP/1.1 204 No Content
```

또는

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "사용자가 성공적으로 삭제되었습니다.",
  "deletedId": 123
}
```

### 코드 예시

```javascript
// Fetch API
fetch('https://api.example.com/users/123', {
  method: 'DELETE'
})
  .then(response => {
    if (response.ok) {
      console.log('삭제 성공');
    }
  });

// Axios
axios.delete('https://api.example.com/users/123')
  .then(response => console.log('삭제 성공'))
  .catch(error => console.error('삭제 실패:', error));
```

### 멱등성

```
첫 번째 DELETE 요청:
DELETE /api/users/123 → ✅ 삭제 성공 (200 OK)

두 번째 DELETE 요청:
DELETE /api/users/123 → ✅ 이미 없음 (404 Not Found)

세 번째 DELETE 요청:
DELETE /api/users/123 → ✅ 여전히 없음 (404 Not Found)

→ 결과는 항상 동일: "리소스가 없는 상태"
→ 멱등성을 만족 ✅
```

---

## 🎯 GET vs POST 심층 비교

### 멱등성 (Idempotent)

**동일한 연산을 여러 번 수행해도 동일한 결과가 나오는 성질**

#### GET의 멱등성

```
첫 번째 요청:
GET /api/users/123 → { name: "홍길동", age: 30 }

두 번째 요청:
GET /api/users/123 → { name: "홍길동", age: 30 }

백 번째 요청:
GET /api/users/123 → { name: "홍길동", age: 30 }

→ 항상 동일한 결과 ✅
→ 서버 상태를 변경하지 않음
```

#### POST의 비멱등성

```
첫 번째 요청:
POST /api/orders → { orderId: 1, total: 50000 }

두 번째 요청:
POST /api/orders → { orderId: 2, total: 50000 }

세 번째 요청:
POST /api/orders → { orderId: 3, total: 50000 }

→ 요청마다 새로운 주문 생성 ❌
→ 결과가 매번 다름
```

### 안전성 (Safe)

**서버의 상태를 변경하지 않는 성질**

```
GET    → ✅ 안전함 (조회만 함)
POST   → ❌ 안전하지 않음 (생성)
PUT    → ❌ 안전하지 않음 (수정)
PATCH  → ❌ 안전하지 않음 (수정)
DELETE → ❌ 안전하지 않음 (삭제)
```

### HTTP Method별 멱등성과 안전성

|Method|멱등성|안전성|설명|
|---|---|---|---|
|**GET**|✅|✅|조회만 하므로 둘 다 만족|
|**POST**|❌|❌|매번 새 리소스 생성|
|**PUT**|✅|❌|같은 데이터로 여러 번 요청해도 결과 동일|
|**PATCH**|⚠️|❌|구현에 따라 다름|
|**DELETE**|✅|❌|여러 번 삭제해도 "없는 상태"로 동일|

### 상세 비교표

|비교 항목|GET|POST|
|---|---|---|
|**용도**|조회 (Read)|생성 (Create)|
|**데이터 위치**|URL (Query String)|Body|
|**데이터 노출**|⚠️ URL에 노출|✅ Body에 숨김|
|**데이터 길이**|⚠️ 제한 있음|✅ 제한 없음|
|**캐싱**|✅ 가능|❌ 불가능|
|**브라우저 기록**|✅ 남음|❌ 안 남음|
|**북마크**|✅ 가능|❌ 불가능|
|**뒤로가기**|✅ 안전|⚠️ 재전송 경고|
|**새로고침**|✅ 안전|⚠️ 중복 전송 위험|
|**멱등성**|✅ 있음|❌ 없음|
|**안전성**|✅ 안전|❌ 안전하지 않음|

### 실전 사용 가이드

#### GET을 사용해야 할 때

```javascript
✅ 게시글 목록 조회
GET /api/posts

✅ 사용자 정보 조회
GET /api/users/123

✅ 검색 (간단한 쿼리)
GET /api/search?q=nextjs

✅ 필터링
GET /api/products?category=electronics&price_max=50000
```

#### POST를 사용해야 할 때

```javascript
✅ 회원가입
POST /api/users

✅ 로그인
POST /api/auth/login

✅ 주문 생성
POST /api/orders

✅ 댓글 작성
POST /api/posts/123/comments

✅ 복잡한 검색 (많은 조건)
POST /api/search
{
  "filters": { ... },
  "sort": { ... },
  "pagination": { ... }
}

✅ 파일 업로드
POST /api/upload
```

---

## 📊 전체 HTTP Method 비교

### 빠른 참조표

|Method|용도|멱등성|안전성|캐싱|Body|
|---|---|---|---|---|---|
|**GET**|조회|✅|✅|✅|❌|
|**POST**|생성|❌|❌|❌|✅|
|**PUT**|전체 수정/생성|✅|❌|❌|✅|
|**PATCH**|부분 수정|⚠️|❌|❌|✅|
|**DELETE**|삭제|✅|❌|❌|❌*|

*DELETE는 Body를 사용할 수 있지만 일반적으로 사용하지 않음

### CRUD 매핑

|CRUD|HTTP Method|예시|
|---|---|---|
|**Create**|POST|`POST /api/users`|
|**Read**|GET|`GET /api/users/123`|
|**Update**|PUT / PATCH|`PUT /api/users/123`|
|**Delete**|DELETE|`DELETE /api/users/123`|

### RESTful API 설계 예시

```
리소스: 사용자 (Users)

┌────────────────────────────────────────────────┐
│ 목록 조회                                       │
│ GET /api/users                                 │
│ → 모든 사용자 목록 반환                          │
├────────────────────────────────────────────────┤
│ 상세 조회                                       │
│ GET /api/users/123                             │
│ → ID가 123인 사용자 정보 반환                    │
├────────────────────────────────────────────────┤
│ 생성                                           │
│ POST /api/users                                │
│ → 새로운 사용자 생성                            │
├────────────────────────────────────────────────┤
│ 전체 수정                                       │
│ PUT /api/users/123                             │
│ → ID가 123인 사용자 정보 전체 교체              │
├────────────────────────────────────────────────┤
│ 부분 수정                                       │
│ PATCH /api/users/123                           │
│ → ID가 123인 사용자 정보 일부 수정              │
├────────────────────────────────────────────────┤
│ 삭제                                           │
│ DELETE /api/users/123                          │
│ → ID가 123인 사용자 삭제                        │
└────────────────────────────────────────────────┘
```

---

## 🎨 실전 시나리오

### 시나리오 1: 전자상거래

```javascript
// 1. 상품 목록 조회
GET /api/products?category=electronics&page=1

// 2. 상품 상세 조회
GET /api/products/789

// 3. 장바구니에 추가
POST /api/cart
{
  "productId": 789,
  "quantity": 2
}

// 4. 장바구니 수량 변경
PATCH /api/cart/items/123
{
  "quantity": 3
}

// 5. 주문 생성
POST /api/orders
{
  "items": [...],
  "shippingAddress": {...}
}

// 6. 주문 취소
DELETE /api/orders/456
```

### 시나리오 2: 소셜 미디어

```javascript
// 1. 피드 조회
GET /api/feed?page=1&limit=20

// 2. 게시글 작성
POST /api/posts
{
  "content": "안녕하세요!",
  "images": [...]
}

// 3. 게시글 수정 (전체)
PUT /api/posts/123
{
  "content": "수정된 내용",
  "images": [...]
}

// 4. 좋아요 추가
POST /api/posts/123/like

// 5. 댓글 작성
POST /api/posts/123/comments
{
  "content": "좋은 글이네요!"
}

// 6. 게시글 삭제
DELETE /api/posts/123
```

### 시나리오 3: 사용자 관리

```javascript
// 1. 회원가입
POST /api/auth/register
{
  "email": "user@example.com",
  "password": "password123",
  "name": "홍길동"
}

// 2. 로그인
POST /api/auth/login
{
  "email": "user@example.com",
  "password": "password123"
}

// 3. 프로필 조회
GET /api/users/me

// 4. 프로필 이미지만 변경
PATCH /api/users/me
{
  "profileImage": "https://..."
}

// 5. 비밀번호 변경
PATCH /api/users/me/password
{
  "currentPassword": "old",
  "newPassword": "new"
}

// 6. 회원탈퇴
DELETE /api/users/me
```

---

## 💡 Best Practices

### 1. 올바른 HTTP Method 선택

```
✅ 좋은 예:
GET    /api/users        → 목록 조회
GET    /api/users/123    → 상세 조회
POST   /api/users        → 생성
PUT    /api/users/123    → 전체 수정
PATCH  /api/users/123    → 부분 수정
DELETE /api/users/123    → 삭제

❌ 나쁜 예:
GET /api/createUser      → POST를 사용하세요
GET /api/deleteUser/123  → DELETE를 사용하세요
POST /api/getUsers       → GET을 사용하세요
```

### 2. URL 설계

```
✅ 명사 사용 (리소스 중심)
/api/users
/api/products
/api/orders

❌ 동사 사용 (행동 중심)
/api/getUsers
/api/createProduct
/api/deleteOrder
```

### 3. 상태 코드 사용

|Method|성공 시|실패 시|
|---|---|---|
|**GET**|200 OK|404 Not Found|
|**POST**|201 Created|400 Bad Request|
|**PUT**|200 OK|404 Not Found|
|**PATCH**|200 OK|404 Not Found|
|**DELETE**|204 No Content|404 Not Found|

### 4. 보안

```
✅ HTTPS 사용
✅ 인증 토큰은 Header에
✅ 민감 정보는 POST/PUT/PATCH의 Body에
✅ CORS 설정
✅ Rate Limiting

❌ GET으로 민감 정보 전송
❌ 인증 없이 수정/삭제 허용
```

---

## ❓ 자주 묻는 질문

### Q1. GET과 POST 중 어떤 것을 사용해야 할까요?

```
✅ GET을 사용하세요:
- 데이터 조회
- 검색 (간단한 쿼리)
- 필터링
- 페이지네이션
- 캐싱이 필요한 경우

✅ POST를 사용하세요:
- 데이터 생성
- 로그인/회원가입
- 파일 업로드
- 복잡한 검색 조건
- 민감한 정보 전송
```

### Q2. PUT과 PATCH의 차이는 무엇인가요?

```
PUT:
- 리소스 전체 교체
- 누락된 필드는 null
- 멱등성 있음

PATCH:
- 리소스 일부만 수정
- 누락된 필드는 유지
- 멱등성 구현에 따라 다름

예시:
{ name: "홍길동", age: 30, email: "hong@example.com" }

PUT { age: 31 }
→ { age: 31, name: null, email: null }

PATCH { age: 31 }
→ { name: "홍길동", age: 31, email: "hong@example.com" }
```

### Q3. DELETE 요청에 Body를 사용할 수 있나요?

```
기술적으로는 가능하지만, 권장하지 않습니다.

✅ 권장:
DELETE /api/users/123

❌ 비권장:
DELETE /api/users
Body: { id: 123 }

이유:
- RESTful 관례에 맞지 않음
- 일부 프록시/클라이언트가 무시할 수 있음
- URL로 충분히 표현 가능
```

### Q4. 멱등성이 왜 중요한가요?

```
멱등성이 있으면:
✅ 네트워크 오류 시 안전하게 재시도 가능
✅ 캐싱 전략 수립 가능
✅ 예측 가능한 API 동작

예시:
PUT /api/users/123 { name: "홍길동" }
→ 네트워크 오류로 응답 못 받음
→ 다시 요청해도 안전 ✅ (결과 동일)

POST /api/orders { ... }
→ 네트워크 오류로 응답 못 받음
→ 다시 요청하면 중복 주문 발생 ⚠️
```

---

## 🎓 요약

### HTTP Method 한눈에 보기

```
GET     → 🔍 조회 (안전, 멱등, 캐시)
POST    → ➕ 생성 (비안전, 비멱등, 노캐시)
PUT     → 🔄 전체 수정 (비안전, 멱등, 노캐시)
PATCH   → ✏️ 부분 수정 (비안전, 비멱등*, 노캐시)
DELETE  → 🗑️ 삭제 (비안전, 멱등, 노캐시)

*PATCH의 멱등성은 구현에 따라 다름
```

### 선택 가이드

```
데이터를 조회하고 싶다
→ GET

데이터를 생성하고 싶다
→ POST

데이터 전체를 바꾸고 싶다
→ PUT

데이터 일부만 바꾸고 싶다
→ PATCH

데이터를 삭제하고 싶다
→ DELETE
```

### 핵심 원칙

1. **의미에 맞는 Method 사용**: GET은 조회, POST는 생성
2. **RESTful 설계**: 명사 기반 URL + HTTP Method
3. **멱등성 고려**: 안전한 재시도 가능하도록
4. **보안**: HTTPS + 적절한 인증/인가
5. **표준 상태 코드**: 일관된 응답

---

## 🔗 추가 리소스

- [MDN: HTTP Request Methods](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods)
- [REST API Tutorial](https://restfulapi.net/)
- [HTTP 상태 코드](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)

---

> 💡 **TIP**: API를 설계할 때는 항상 "이 요청이 서버의 상태를 변경하는가?"를 먼저 생각하세요. 변경하지 않으면 GET, 변경한다면 POST/PUT/PATCH/DELETE를 사용하면 됩니다!