
> 이 문서는 대화 세션 중 공개 API 문서(docs.topview.ai), 웹 검색, 리뷰 사이트, 그리고 실제 사용 화면 스크린샷/트랜스크립트를 기반으로 정리한 TopView.ai 분석 내용입니다. 작성 기준일: 2026-07-27

---

## 1. 서비스 개요

- **설립**: 2022년, 싱가포르
- **포지셔닝**: 생성형 AI 기반 영상 제작 자동화 플랫폼 ("AI Video Agent")
- **핵심 가치 제안**: 편집 지식 없이 제품 링크/이미지/텍스트만으로 마케팅 영상, 숏폼, 마이크로드라마 등을 생성
- **타겟 사용자**: 이커머스 셀러, DTC 브랜드, 어필리에이트 마케터, 마이크로드라마/숏드라마 제작팀, 에이전시
- **주요 도메인**
    - 웹사이트: https://www.topview.ai
    - API 문서: https://docs.topview.ai
    - API 엔드포인트: https://api.topview.ai
    - Drama Studio: `https://topview.ai/drama-studio/proj_{projectId}?production=ep_{episodeId}`

### 장단점 (리뷰 종합)

|장점|단점|
|---|---|
|Product Avatar, Anyshoot 등 이커머스 특화 기능|아바타 움직임이 다소 로봇 같음|
|5~10배 빠른 제작 속도, 80~90% 비용 절감 주장|AI 생성 티가 남 (트레이닝된 눈에는)|
|초보자 친화적 (편집 지식 불필요)|고객지원/빌링 관련 부정 후기 존재|
|강력하고 잘 문서화된 공개 API|내장 편집기는 기본 기능만 제공|
|UGC 스타일 광고, 숏폼 최적화|세밀한 디테일 제어에는 한계|

---

## 2. 제공 서비스 종류 (공개 API 문서 기준 전체 목록)

### 영상 생성

- **Avatar Marketing Video**: 제품 URL/영상/이미지 → 디지털 휴먼 마케팅 영상 자동 생성
- **Photo Avatar / Avatar4**: 사진 한 장 + TTS 또는 오디오 파일 → 말하는 아바타 영상 (최신 세대 아바타 모델)
- **Video Avatar**
- **Product Avatar (v1/v2/v3)**: 배경 제거 → 제품 이미지 합성 → Image2Video 파이프라인
- **Product Anyshoot**: Product Model(모델 합성), Product Background(배경 합성) - 템플릿/카테고리 지원
- **Text-to-Video / Image-to-Video V2 / Omni Reference**: 텍스트/이미지/레퍼런스 기반 영상 생성, 멀티 AI 모델 지원(Veo, Sora, Seedance, Wan, Kling, Higgsfield 등)
- **Motion Control**: 모션 레퍼런스 영상으로 캐릭터 움직임 제어
- **Video2AIAvatar**

### 이미지 생성/편집

- Text-to-Image, Image Edit (모델: GPT Image 2, Nano Banana 2/Pro 등)
- Image/Video Character Swap (얼굴·캐릭터 교체)
- Image/Video Mask Drawing
- Image Translate
- Remove Background
- Storyboard (스토리 텍스트 → 그리드 스토리보드 이미지, 4/9/25컷 지원)

### 음성/음악

- Voice Clone / Instant Voice Clone (모델: Index TTS, Fish Audio S2 Pro)
- Text2Voice
- AI Music (모델: Topview Music, Minimax Music 2.6 — 가사/스타일 기반, instrumental 옵션)

### 부가 기능

- Scraper (제품 URL 스크래핑 → 소재 자동 추출)
- Boards (워크스페이스 단위 프로젝트 관리, task 필터링, 배치 다운로드/삭제)
- Credit 조회 / Credit 로그 조회 / User Space 조회
- **Drama Studio**: 마이크로드라마/숏드라마 전용 프리프로덕션+제작 통합 에이전트 (스크린샷으로 실사용 확인됨)

---

## 3. 시스템 아키텍처 — API 레벨

### 3.1 인증 구조

- 헤더 2개로 인증: `Topview-Uid` + `Authorization: Bearer <API Key>`
- 웹 대시보드 계정 설정 → API Settings에서 발급
- 웹 UI 크레딧과 API 크레딧 풀 공유 (단, Ultra 등급 크레딧은 API 사용 불가)

### 3.2 공통 패턴: 비동기 Task (Submit → Query)

거의 모든 기능이 동일한 2단계 패턴을 따름:

```
1) POST .../submit  → 즉시 taskId 반환
2) GET  .../query?taskId=xxx  → 폴링 (권장 주기 3~5초, 타임아웃 5분)
```

- Task 상태: `init`(대기) → `running`(처리중) → `success`/`fail`(완료)
- 실패 시 `errorMsg`로 사유 반환, 크레딧은 성공 시에만 차감·실패 시 전액 환불
- 주요 에러 코드: `4000`대(파라미터 오류), `4100`(크레딧 부족), `5000`(서버 오류)

### 3.3 파일 업로드 흐름 (Presigned URL 패턴)

```
① GET /v1/upload/credential?format=xxx  → fileId + S3 presigned uploadUrl 발급
② PUT {uploadUrl}  → 클라이언트가 로컬 파일을 S3에 직접 업로드 (서버 미경유)
③ GET /v1/upload/check?fileId=xxx  → 업로드 성공 여부 확인
```

이후 모든 API는 파일 자체가 아닌 `fileId`를 참조로 주고받음.

### 3.4 Product Avatar 파이프라인 (실제 API 예시 확인됨)

```
productImageFileId
   ↓ (POST /v1/common_task/remove_background/submit)
bgRemovedImageFileId + maskImageFileId
   ↓ (POST /v3/product_avatar/task/image_replace/submit — manual 또는 auto 모드)
     - manual: 4점 좌표(location, 0~1 정규화)로 합성 위치 직접 지정
     - auto: 자연어 프롬프트(imageEditPrompt)로 AI가 자동 배치
imageId
   ↓ (POST /v3/product_avatar/task/image2Video/submit — mode: pro, scriptMode: text/audio)
finishedVideoUrl (립싱크 디지털 휴먼 영상)
```

### 3.5 Avatar Marketing Video 파이프라인

```
제품 URL 또는 로컬 영상/이미지 업로드
   → AI가 자동으로 스크립트/씬 구성 (script list/update API로 사용자 수정 가능)
   → submit → query로 워터마크 있는 저해상도 미리보기 확인
   → 마음에 드는 버전 선택 → export API로 워터마크 없는 HD + 립싱크 최종 영상 생성
```

**미리보기(저해상도) → 선택 → 고해상도 export**의 2단계 렌더링 구조 — 전체 재생성 없이 크레딧 절약.

### 3.6 스토리지 및 동시성

- 생성 결과물(영상/이미지)은 AWS S3(`aigc.s3.amazonaws.com`)에 저장, **presigned URL 7일간만 유효** (직접 다운로드 보관 필요)
- 동시성: 전체 사용자가 공유 리소스 풀 사용, 혼잡 시 우선순위 큐잉 `Business Annual > Business Monthly > Pro Annual > Pro Monthly > Free`

### 3.7 과금 구조

- 크레딧 기반, 결과물 길이(초, 올림)로 정산되는 경우多
- 예: Photo Avatar4 = 0.1 credit/s, Avatar4Fast = 0.06 credit/s, off-peak 시간대 50% 할인
- 무료 가입 시 10 크레딧 제공 (약 짧은 영상 2개 분량)
- 유료 플랜: Free($0) → Pro(~$16/월) → Business(~$40/월), 연간 결제 시 할인

---

## 4. Drama Studio — 실제 UI 흐름 (스크린샷 기반 확인)

### 4.1 전체 프리프로덕션 파이프라인

```
① 스토리 아이디어 입력 (한 줄 컨셉)
   ↓
② 크리에이티브 방향 설정 (대화형 객관식 선택: 타겟관객/장르/톤)
   → 배경 태그, 설정 태그, 핵심 감정 메타데이터로 누적 정리
   ↓
③ 로그라인 확정 (AI 제안 → 승인/수정/재제안)
   ↓
④ 아웃라인 생성 (에피소드 단위)
   → 각 화마다 [단계 목표] [감정 비트] 태그 부여 (감정 곡선 설계)
   → 화별 타겟 길이(초) 배정
   ↓
⑤ 설정 자료 병렬 생성 (장소 / 소품 / 캐릭터 동시 생성)
   → 장소: 조명·색감·분위기 포함 비주얼 묘사
   → 소품: 카테고리 태그 + 극 중 상태 변화 묘사
   → 캐릭터: 신체 스펙, 외형, 의상, 성격 태그, 역할군(male_lead/supporting/antagonist)
   ↓
⑥ 씬 스크립트 생성 (에피소드 순차 진행, 씬→샷 분할)
   → 포맷: [에피소드-씬 | 장소 | 시간대 | 실내외 | 감정톤] + Cast + Story + (action) 지문 + 대사
   → 이 씬/샷 분할 단위 = "비트(Beat)"
─────────────────────────────
   [영상 생성 시작] ← 여기서부터 실제 렌더링 단계
```

### 4.2 실패/오류 사례 (실제 관찰됨)

- 화면상 "6개 에피소드 스크립트 완성" 메시지가 떴음에도, 백엔드에는 **draft 상태로 저장되지 않아 beats=0**으로 표시되는 케이스 발생
- 이 경우 "영상 제작 시작" 버튼이 비활성화됨 → 씬 스크립트 전체 재생성으로 해결
- **UI 표시 상태와 실제 백엔드 커밋 상태가 어긋날 수 있음**을 시사

### 4.3 개요 화면 (프로젝트 대시보드) 구성 요소

- 프로젝트 개요: 로그라인, 원대한 기대(스토리 질문), 에피소드 수/총 러닝타임/등장인물 수/배경 수
- 상단 버튼: `+ 에피소드 추가`, `AI 최적화 개요`(기능 미확인), `스크립트 및 자산 내보내기`, `장면 목록`, `후크 체인`(기능 미확인)
- 에피소드 카드별: 제목, 시놉시스, 러닝타임, `스크립트 완료(N 비트)` 배지, `영상 X/N` 진행률, `스크립트 보기/편집`, `영상 제작 시작` 버튼

### 4.4 비트 단위 영상 생성 화면 구성 요소

- **상단 컨텍스트 태그**: `@Scene: 민준의 방`, `@강민준:` — 앞 단계에서 만든 장소/캐릭터 설정을 프롬프트에 참조 앵커로 삽입 (일관성 유지 메커니즘)
- **프롬프트 영역**:
    - 한국어 씬 스크립트를 AI가 영어 시네마틱 프롬프트로 자동 재작성 (카메라워크·조명·인물 디테일 포함)
    - 원래 한국어 대사는 그대로 인용 삽입 (립싱크/음성 생성용 참조로 추정)
    - 네거티브 프롬프트 자동 포함 (예: 배경음악/효과음 넣지 말 것 지시)
    - `프롬프트 재생성` 버튼, `/` 입력으로 커스텀 프롬프트 프리셋 관리 가능
- **모델/생성 설정**: 모델(예: Seedance 2.0) · 길이(15s) · 해상도(480p) · 생성 개수(×1) 선택, 조합별 크레딧 비용 실시간 표시(예: +7.5)
- **비트 타임라인**: 비트1~N이 개별 카드로 나열(길이 12~15초 가변), `+`로 비트 삽입 가능, `단일 비트` 토글로 개별/연속 재생 전환

---

## 5. 분석 진행 상태 종합

|항목|상태|비고|
|---|---|---|
|UI 흐름|🟢 확보|프리프로덕션 전 과정 + 비트 편집 화면까지 실물 확인|
|서비스 종류|🟢 확보|공개 API 문서 전체 목록 + Drama Studio 실사용 확인|
|시스템 전반 동작 방식|🟡 부분 확보|파이프라인/상태관리/오류사례는 확인, 세부 백엔드 로직은 미확인|
|API 구조|🟡 부분 확보|공개 v1/v3 API(Product Avatar, Avatar Marketing Video, Upload 등)는 확인. Drama Studio 자체 백엔드 API는 미공개|
|렌더링 방식|🟡 부분 확보|멀티모델 선택, 파라미터, S3 저장 구조는 확인. 비트→최종본 합성/립싱크 내부 로직은 미확인|

### 아직 확인되지 않은 부분

- 비트를 이어붙여 최종 에피소드로 합성하는 화면/로직 (이미지1의 "스크립트 및 자산 내보내기" 버튼이 관련될 것으로 추정)
- 음성/더빙이 비트별로 실제로 어떻게 TTS·립싱크로 합성되는지의 화면 단계
- "AI 최적화 개요", "후크 체인" 기능의 실제 동작
- Drama Studio 웹 UI가 공개 문서화된 v1/v3 API와 동일 백엔드를 쓰는지, 별도 내부 API를 쓰는지 여부

---

## 6. 참고 출처

- https://docs.topview.ai/llms.txt (API 문서 전체 인덱스)
- https://docs.topview.ai/docs/getting-started
- https://docs.topview.ai/reference/authentication-1
- https://docs.topview.ai/reference/getting-started-1 (Avatar Marketing Video)
- https://docs.topview.ai/reference/pa3_getting_started (Product Avatar v3)
- https://docs.topview.ai/docs/billing-rules
- https://docs.topview.ai/docs/concurrency-and-storage
- https://docs.topview.ai/reference/upload-api-usage
- https://www.topview.ai/openapi
- https://skywork.ai (Topview 심층 리뷰, 한국어)
- https://filmora.wondershare.kr (Topview AI 리뷰)
- https://creati.ai/ko/ai-tools/topview-ai (기능/요금제 요약)
- 실사용 스크린샷 2장 (Drama Studio 개요 화면, 비트 편집 화면) — 사용자 제공