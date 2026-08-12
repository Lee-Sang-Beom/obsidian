
## 1. 한 줄 정의

Topview.ai는 **마케팅 영상, 상품 광고, AI Avatar, 숏드라마/마이크로드라마, 이미지/비디오/오디오 생성 작업을 하나의 워크스페이스에서 기획·생성·편집·관리·자동화하는 AI 제작 플랫폼**이다.

단순한 “AI 영상 생성기”라기보다, 다음 요소를 묶은 통합 제작 환경으로 보는 것이 정확하다.

| 구성 요소 | 역할 |
|---|---|
| Video Agent | 아이디어, reference, 상품 소재를 바탕으로 영상 제작 흐름을 자동 제안/실행 |
| Canvas | 장면, 에셋, 샷, 스타일을 자유롭게 조합하고 편집하는 제작 공간 |
| Drama Studio | 소설, 스크립트, plot 기반 숏드라마/마이크로드라마 제작 도구 |
| Board | 이미지, 비디오, 오디오, Avatar 결과물을 한곳에서 생성·비교·관리하는 에셋 보드 |
| API | Topview 기능과 여러 AI 모델을 프로그램 방식으로 호출하는 자동화 인터페이스 |
| Team/Workspace | 팀 단위 credit, project, asset, 권한 관리 |

## 2. 서비스 포지셔닝

Topview.ai의 핵심 포지션은 **AI Video Agent for film, drama, marketing, ecommerce**에 가깝다. 공식 사이트는 social ads, ecommerce creatives, avatar videos, short drama, AI film, marketing video를 주요 사용 맥락으로 제시한다.

Topview가 해결하려는 문제는 다음과 같다.

| 문제 | Topview의 접근 |
|---|---|
| 영상 제작에 촬영, 배우, 편집자, 스튜디오가 필요함 | AI Avatar, Product Avatar, URL-to-Video, template/reference 기반 생성으로 제작 단계를 줄임 |
| 상품 SKU나 광고 variant가 많아 수작업 제작이 어려움 | product URL, image, script, audio를 입력해 대량 생성 workflow를 제공 |
| 영상 모델이 너무 많고 모델별 API/과금/파라미터가 다름 | 여러 외부 모델과 Topview native 기능을 하나의 Board/API/credit 체계로 묶음 |
| 캐릭터, 장면, 목소리, 스타일 일관성 유지가 어려움 | reference, Canvas, Drama Studio, Board를 통해 반복 사용 가능한 에셋을 관리 |
| 팀 제작에서 결과물 비교/승인/재사용이 번거로움 | Board와 team workspace로 결과물을 묶고 관리 |

## 3. 핵심 사용자와 사용 시나리오

| 사용자 유형           | 주요 니즈                                        | Topview에서 쓰는 기능                                          |
| ---------------- | -------------------------------------------- | -------------------------------------------------------- |
| 이커머스/DTC 브랜드     | 상품 상세페이지, 광고 소재, UGC 스타일 영상 대량 제작            | URL-to-Video, Product Avatar, Product Anyshoot, AI Board |
| 광고/마케팅 에이전시      | 여러 캠페인 variant와 reference 기반 광고 제작           | Video Agent, Canvas, Board, API                          |
| 숏폼 크리에이터         | viral hook, meme, POV, storytelling 영상 제작    | Video Agent, AI Video, AI Image, Audio                   |
| 영화/드라마/마이크로드라마 팀 | 캐릭터와 장면이 이어지는 숏드라마 제작                        | Drama Studio, Canvas, Storyboard, AI Video               |
| 개발팀/자동화팀         | 내부 서비스나 production pipeline에 영상/이미지 생성 연결    | Topview API, Upload API, Submit/Query task, Board API    |
| 글로벌 마케팅팀         | 다국어 voiceover, Avatar, localized creative 제작 | AI Avatar, AI Audio, Instant Voice Clone, Video Lip Sync |

## 4. 제품 구조

### 4.1 Video Agent

Video Agent는 사용자가 자연어 prompt, reference image/video, 상품 이미지 등을 넣으면 영상 제작 방향을 잡아주는 진입점이다. 공식 사이트는 “Create Any Video, Just Tell Your Agent”라는 흐름으로 Video Agent를 소개한다.

주요 역할은 다음과 같다.

| 역할 | 설명 |
|---|---|
| 아이디어 구체화 | 짧은 prompt나 상품 정보를 영상 콘셉트로 확장 |
| reference 활용 | reference image/video를 first frame, last frame, motion reference 등으로 사용 |
| 모델 선택 | Seedance, Kling, Veo 등 상황에 맞는 모델을 선택하거나 사용자가 선택 |
| 샷 구성 | 여러 장면/샷으로 나뉜 영상 구조를 생성 |
| 제작 흐름 실행 | 이미지, 영상, 오디오, Avatar 생성 작업을 순차적으로 실행 |

### 4.2 Canvas

Canvas는 Agent가 만든 장면과 에셋을 사람이 다시 조정할 수 있는 제작 공간이다. 공식 설명상 Canvas는 장면을 제안하고, 에셋을 만들고, production flow를 실행하며, voice/character/visual identity의 일관성을 유지하는 데 초점이 있다.

Canvas는 “단일 prompt -> 결과물”보다 더 긴 제작 흐름에 적합하다.

| 사용 예 | 설명 |
|---|---|
| 여러 샷의 광고 영상 | hook, product demo, CTA 장면을 나눠 구성 |
| reference 기반 영상 | 특정 이미지/비디오의 분위기, 구도, 움직임을 재사용 |
| 캐릭터 일관성 | 같은 인물/Avatar/스타일을 여러 장면에서 유지 |
| 반복 수정 | 생성 결과를 비교하고 특정 장면만 다시 생성 |

### 4.3 Drama Studio

Drama Studio는 novel, script, plot 기반의 마이크로드라마/숏드라마 제작에 특화된 영역이다. 일반 광고 영상보다 캐릭터, 장면, 서사 구조, episode 단위 흐름이 중요할 때 사용한다.

| 기능 관점 | 설명 |
|---|---|
| 입력 | novel, script, plot, story idea |
| 출력 | 숏드라마용 장면, storyboard, 영상 에셋 |
| 강점 | 캐릭터/장면/비주얼/사운드의 일관성 유지 |
| 적합한 작업 | vertical drama, short-form series, micro-drama, film-style scene |

주의할 점은 공개 API 문서에 Drama Studio 내부의 모든 상태 저장, beat 생성, scene editing API가 그대로 공개되어 있는 것은 아니라는 점이다. 따라서 “웹 UI에서 가능한 작업”과 “공개 API로 자동화 가능한 작업”은 분리해서 봐야 한다.

### 4.4 Board

Board는 Topview의 에셋 관리 중심이다. 공식 사이트는 Board를 이미지, 비디오, 오디오, Avatar 모델을 한곳에서 사용하고 결과물을 review, comparison, reuse할 수 있는 공간으로 설명한다.

API 문서상 Board 관련 endpoint도 존재한다.

| Board 기능 | API/업무 의미 |
|---|---|
| Board 목록 조회 | 계정 내 작업 공간 목록 확인 |
| Board task 목록 조회 | 특정 Board 안의 이미지/비디오/오디오 결과물 확인 |
| Board task 상세 조회 | 산출물 URL, fileId, status 등 확인 |
| batch delete | 여러 task 삭제 |
| batch download | 여러 결과물을 ZIP으로 묶어 다운로드 |

Board는 단순 히스토리 목록이 아니라, 생성 결과물을 운영 단위로 묶고 재사용하는 저장소에 가깝다.

### 4.5 API

Topview API는 Topview 웹 기능을 programmatic workflow로 확장하는 수단이다. 공식 API 소개는 Topview API를 여러 AI video/image model과 Topview native API를 하나의 API key, 하나의 billing account로 호출하는 구조로 설명한다.

API의 핵심 특징은 다음과 같다.

| 특징 | 설명 |
|---|---|
| 단일 계정/과금 | Topview 계정 credit을 API에서도 사용 |
| 외부 모델 통합 | Veo, Sora, Seedance, Wan, Kling, Flux 등 여러 모델명을 Topview API 안에서 사용 |
| Topview native workflow | Avatar, URL-to-Video, Product Avatar, Product Anyshoot 등 자체 workflow 제공 |
| 비동기 task 패턴 | submit endpoint로 `taskId`를 받고 query endpoint로 polling |
| fileId 기반 파일 처리 | upload credential을 받아 파일을 올리고, 이후 API에는 `fileId`를 전달 |
| Board 연동 | 생성 결과를 Board task로 관리 가능 |

## 5. 기능 영역별 분석

### 5.1 AI Video

AI Video 영역은 text-to-video, image-to-video, video edit, motion control, character swap, object remove, mask drawing 등으로 나뉜다.

| 기능 | 설명 | 비고 |
|---|---|---|
| Text-to-Video | prompt에서 영상 생성 | 모델별 duration, resolution, aspect ratio 제약이 다름 |
| Image-to-Video | 시작 이미지 또는 reference image 기반 영상 생성 | 상품/캐릭터 일관성에 유용 |
| Omni Reference | 여러 reference를 사용한 영상 생성 | Topview 문서상 모델 교체/폐기 이슈가 있을 수 있음 |
| Motion Control | 움직임/카메라 제어 | Kling, Higgsfield 등 모델별 성격 차이 |
| Video Character Swap | 영상 속 인물/캐릭터 교체 | submit/query task 구조 |
| Video Object Remove | selectionBox 등으로 지정한 객체 제거 | 완료 후 결과 비디오 URL 반환 |
| Video Process / Mask Drawing | 특정 영상 처리나 mask 기반 편집 | 작업 단계와 taskId 관리가 중요 |

### 5.2 AI Image

AI Image 영역은 이미지 생성, 편집, product photo, inpaint, character swap, mask drawing, translate 등으로 구성된다.

| 기능 | 설명 |
|---|---|
| Text-to-Image | prompt 기반 이미지 생성 |
| Image Edit | 기존 이미지 수정 |
| Image Character Swap | 이미지 속 인물/캐릭터를 reference 인물로 교체 |
| Image Mask Drawing | 이미지 안의 영역을 mask로 지정 |
| Image Translate | 이미지 내 텍스트 번역 후 이미지 재생성 |
| Product Photography | 상품 이미지를 광고/라이프스타일 컷으로 변환 |
| Virtual Try-on | 의류/상품 착용 또는 배치 이미지 생성 |

이미지 기능은 Product Avatar, Product Anyshoot, URL-to-Video 같은 상위 workflow의 전처리/소재 생성 단계로도 사용된다.

### 5.3 AI Avatar

Avatar는 Topview의 중요한 차별화 영역이다. 단순 talking head가 아니라 product demo, UGC 광고, lip sync, custom avatar까지 포함한다.

| 기능 | 설명 |
|---|---|
| Photo Avatar / Avatar4 | 사진 또는 avatar template을 사용해 말하는 avatar 영상 생성 |
| Product Avatar | avatar가 실제 상품을 들고 시연하는 형태의 영상 생성 |
| Video Avatar | 텍스트/오디오 기반 avatar video 생성 |
| Design My Avatar | 사용자 맞춤 avatar 생성 |
| Video Lip Sync | 기존 영상/Avatar의 입 모양과 음성을 동기화 |
| Custom Avatar 관리 | custom avatar 생성, 목록 조회, 삭제 |

API 관점에서는 `scriptMode=text`와 `scriptMode=audio`를 구분해야 한다. text 모드는 TTS를 사용하고, audio 모드는 업로드한 오디오 fileId를 사용한다.

### 5.4 Product / Ecommerce Workflow

Topview는 이커머스와 상품 광고 workflow를 강하게 전면에 둔다.

| 기능 | 설명 | 적합한 사용 |
|---|---|---|
| URL-to-Video | 상품 URL에서 제목, copy, hero shot 등을 추출해 광고 영상 생성 | 쇼핑몰/마켓플레이스 SKU 광고 |
| Product Avatar | avatar가 상품을 들고 설명/시연 | UGC 스타일 product demo |
| Product Anyshoot | 상품을 lifestyle scene, virtual try-on, mockup에 배치 | 상품 사진/광고 이미지 |
| Materials-to-Video | 이미지, script, audio를 넣어 marketing video 생성 | catalog-scale 영상 제작 |

이 영역은 “모델 하나를 호출한다”기보다, scraping, image generation, avatar, voice, video generation, export가 결합된 workflow로 보는 것이 맞다.

### 5.5 AI Audio / Voice

Topview의 Audio 기능은 voiceover, instant voice clone, AI music을 포함한다.

| 기능 | 설명 |
|---|---|
| AI Voiceover | script를 자연스러운 음성으로 변환 |
| Instant Voice Clone | reference audio와 text를 사용해 음성 복제 결과 생성 |
| AI Music | lyrics/styles/referenceAudio 기반 음악 생성 |
| Avatar voiceModel | Avatar 생성 시 ElevenLabs/MiniMax 등 voice model 선택 가능 |

Voice 기능은 단독 결과물로도 쓰이지만, Avatar/Video workflow 안에서 narration, lip sync, localized creative에 연결된다.

### 5.6 Storyboard

Storyboard는 story text와 reference image를 기반으로 storyboard grid preview image를 생성하는 기능이다. 이후 영상 생성 단계와 연결해 “blind video generation”의 실패 비용을 줄이는 역할을 한다.

| 항목 | 설명 |
|---|---|
| 입력 | story, model, aspectRatio, resolution, gridMode, referenceFileIds |
| 출력 | storyboard image fileId, URL, costCredit |
| 역할 | 장면 구조를 먼저 확정하고 이후 영상 생성을 제어 |
| 관련 모델 | GPT Image 2, Nano Banana 계열 등 |

## 6. API 운영 구조

### 6.1 인증

Topview API는 일반적으로 다음 header를 사용한다.

| Header | 의미 |
|---|---|
| `Topview-Uid` | Topview 사용자 ID |
| `Authorization` | `Bearer <API Key>` 형식의 API Key |

API Key는 Topview 계정 생성 후 API Settings에서 확인/복사하는 흐름으로 안내된다.

### 6.2 공통 응답 구조

공개 API 문서에서 반복되는 응답 구조는 다음 형태다.

| Property | 의미 |
|---|---|
| `code` | Topview 비즈니스 응답 코드 |
| `message` | 응답 메시지 또는 오류 설명 |
| `result` | 실제 결과 객체 |

주의할 점은 HTTP status만으로 성공/실패를 판단하지 말고, JSON body의 `code`, task의 `status`, `errorMsg`까지 함께 확인해야 한다는 점이다.

### 6.3 비동기 Submit / Query 패턴

대부분의 생성형 API는 비동기 구조다.

```text
1. Submit Task 호출
2. 응답에서 taskId 수신
3. Query Task를 주기적으로 polling
4. status가 success이면 output URL/fileId 확인
5. status가 fail이면 errorMsg/errorCode 확인
6. costCredit으로 실제 차감 credit 확인
```

| 상태 | 의미 |
|---|---|
| `init` | 작업 생성/대기 |
| `running` | 처리 중 |
| `success` | 성공 |
| `fail` | 실패 |

실무에서는 polling interval, timeout, retry, idempotency, 사용자 취소/재시도 UX를 별도로 설계해야 한다.

### 6.4 Upload / fileId 패턴

이미지, 영상, 오디오 입력이 필요한 API는 원본 파일을 request body에 직접 넣기보다 `fileId`를 참조하는 구조가 많다.

```text
1. Get Upload Credential 호출
2. fileId, fileName, uploadUrl 수신
3. uploadUrl로 실제 파일 업로드
4. 필요 시 Upload Check 수행
5. 생성 API에 fileId 전달
```

이 구조 때문에 Topview 연동 시스템은 자체 파일 저장소와 Topview fileId 매핑 테이블을 갖는 것이 좋다.

### 6.5 결과 URL과 저장 정책

공식 문서의 Concurrency and Storage 안내에 따르면, 별도 명시가 없는 경우 API로 생성된 video/image URL은 7일 동안만 유효하다. 따라서 운영 서비스에서 결과물을 계속 노출해야 한다면 Topview 결과 URL을 그대로 장기 저장소처럼 쓰면 안 된다.

권장 흐름은 다음과 같다.

```text
1. Query Task에서 결과 URL 수신
2. 즉시 자체 storage로 복사
3. 자체 CDN/DB URL로 사용자에게 제공
4. Topview fileId와 원본 URL은 추적/재처리용 metadata로 보관
```

## 7. 과금, credit, concurrency

### 7.1 Credit 구조

Topview API credit은 Topview 웹 계정 credit과 공유된다. 기능별 가격은 각 API 문서 또는 가격표 기준으로 확인해야 하며, 일부 API는 웹 사용과 API 과금 방식이 조금 다를 수 있다.

중요한 변경/주의 사항은 다음과 같다.

| 항목 | 분석 |
|---|---|
| 웹 계정 credit 공유 | API 사용도 Topview 계정 credit을 사용 |
| 기능별 단가 상이 | 모델, resolution, duration, native audio 여부에 따라 차감량이 달라짐 |
| 일부 API 과금 예외 | 웹과 API 과금 방식이 약간 다를 수 있음 |
| Ultra credit 제한 | Billing Rules 문서상 Ultra monthly credit은 API request에 사용할 수 없다고 안내됨 |
| 실제 차감 확인 | 생성 완료 후 query 응답의 `costCredit`을 저장하는 것이 안전 |

### 7.2 Concurrency

공식 문서 기준으로 모든 사용자는 공통 resource pool을 공유하며, 혼잡 시 plan 우선순위에 따라 queue 처리된다. Concurrency and Storage 문서는 queue 우선순위를 Business Annual > Business Monthly > Pro Annual > Pro Monthly > Free users로 안내한다.

가격 페이지에서는 plan별 concurrent task 수가 별도로 노출된다.

| Plan | 공개 가격표상 concurrent task 예시 |
|---|---|
| Pro | 4 concurrent tasks |
| Business | 8 concurrent tasks |
| Ultra | 12 concurrent tasks |
| Team | 30 total paid concurrent tasks로 표시되는 구간 있음 |

정확한 plan/가격/할인/credit 수량은 자주 바뀔 수 있으므로, 문서에는 반드시 기준일과 출처를 남겨야 한다.

## 8. 외부 모델/서비스 의존성

Topview는 자체 모델만으로 동작하는 폐쇄형 서비스가 아니다. 공식 API 페이지는 Topview API가 Veo, Sora, Seedance, Wan, Kling, Higgsfield, Flux와 Topview native API를 한 API로 호출하게 해준다고 설명한다.

### 8.1 외부 AI 모델

| 영역 | Topview에 노출된 모델 | 외부 제공사/출처 | 근거 수준 |
|---|---|---|---|
| Video | Veo | Google | 높음 |
| Video | Sora | OpenAI | 높음 |
| Video | Seedance / Seedream | ByteDance | 높음 |
| Video | Wan | Alibaba 계열 | 높음 |
| Video | Kling | Kuaishou / Kling AI | 높음 |
| Video | Hailuo / MiniMax-Hailuo | MiniMax | 높음 |
| Video | Vidu | Vidu Studio | 높음 |
| Video | Runway | Runway | 중간~높음 |
| Image | GPT Image | OpenAI | 높음 |
| Image | Flux | Black Forest Labs | 높음 |
| Image | Nano Banana | 제공사명 공개는 제한적 | 중간 |
| Image | Ideogram | Ideogram | 중간~높음 |
| Audio | Minimax Music | MiniMax | 높음 |
| Voice | ElevenLabs | ElevenLabs | 높음 |
| Voice | Fish Audio S2 Pro | Fish Audio | 높음 |

다만 “Topview backend가 실제로 해당 원제공사의 API를 직접 호출한다”는 내부 구현 경로는 공개 문서만으로 확정할 수 없다. 공개적으로 확인 가능한 것은 **Topview가 해당 모델명을 서비스/API에 노출하고, Topview 계정/Board/과금 체계에서 사용할 수 있게 제공한다**는 점이다.

### 8.2 인프라 의존성

| 인프라/서비스 | 근거 | 해석 |
|---|---|---|
| AWS S3 | upload credential, S3 path, `aigc.s3.amazonaws.com` 예시 | 업로드 파일/산출물 저장에 S3 사용 가능성이 높음 |
| AWS CloudFront | `needCloudFrontUrl` 옵션 | 결과물 다운로드/가속 URL 제공 |
| YouTube API Services | Terms에서 YouTube API Services 통합 언급 | YouTube 관련 가져오기/분석/연동 기능 가능성 |
| third-party payment services | Terms의 payment 처리 문구 | 결제 처리에 외부 결제 provider 사용 가능성 |

## 9. API 범위와 주요 카테고리

현재 정리된 API reference는 사용자가 최초로 준 링크 묶음을 기준으로 확장된 문서다. Topview 전체 공개 API를 모두 포괄하려면 다음 카테고리까지 함께 봐야 한다.

| 카테고리 | 대표 기능 |
|---|---|
| Credit / Logs / Space | credit 잔액, credit log, storage 사용량 |
| Upload | upload credential, upload check, fileId 관리 |
| Boards | Board 목록, task 목록, 상세, 삭제, batch download |
| Storyboard | storyboard submit/query |
| AI Music | AI music submit/query |
| Voice | Instant Voice Clone, voice query, TTS |
| Image | character swap, mask drawing, translate, text-to-image, image edit, remove background |
| Video | character swap, process, mask drawing, object remove, text-to-video, image-to-video, motion control |
| Avatar | Photo Avatar, Avatar4, custom avatar, public avatar, avatar category |
| Product | Product Avatar v1/v2/v3, Product Anyshoot, Product Background |
| Marketing Video | URL-to-Video, Materials-to-Video, script update, export, task list/delete |
| Utility | Scraper, Caption List, Notice URL check |

## 10. 서비스 강점

| 강점 | 설명 |
|---|---|
| 제작 workflow 통합 | prompt, reference, image, video, audio, avatar, product URL을 한 흐름에서 다룸 |
| 외부 모델 통합 | 여러 AI 모델을 별도 vendor integration 없이 하나의 Topview 계정에서 사용 |
| 마케팅/커머스 특화 | UGC 광고, product demo, SKU variant 제작에 초점 |
| Avatar/Product 기능 | 상품을 들고 시연하는 Avatar workflow가 강점 |
| Board 기반 관리 | 생성 결과를 asset/task 단위로 비교하고 재사용 가능 |
| API 자동화 | 대량 제작, 내부 CMS/상품 DB 연동, batch workflow에 적합 |
| 팀 기능 | shared asset library, credit usage control, project permission 관리 가능 |

## 11. 한계와 리스크

| 리스크 | 설명 | 대응 |
|---|---|---|
| 모델/가격 변동 | Topview가 외부 모델을 빠르게 추가/교체하므로 model name, 지원 파라미터, 단가가 바뀔 수 있음 | 모델/가격 정보를 코드에 하드코딩하지 않고 설정화 |
| 결과 URL 만료 | API 산출물 URL은 별도 명시가 없으면 7일 유효 | 결과물을 자체 storage로 즉시 복사 |
| 외부 모델 장애 | 특정 외부 모델만 지연/실패할 수 있음 | 모델별 fallback, retry, timeout 설계 |
| 과금 예외 | 웹과 API 과금 방식이 일부 다를 수 있음 | submit 전 예상 비용, query 후 `costCredit` 저장 |
| Ultra credit 혼동 | 가격표와 Billing Rules를 함께 봐야 하며 Ultra monthly credit은 API 사용 제한이 있음 | API 사용 가능 credit 종류를 운영 정책에 명시 |
| 공개 API와 웹 UI 차이 | Drama Studio/Canvas의 모든 내부 기능이 API로 공개된 것은 아님 | “웹 UI 가능”과 “API 자동화 가능”을 분리 |
| 권리/동의 문제 | 인물, 음성, 상품 이미지, YouTube/third-party reference 사용 시 권리 이슈 발생 가능 | consent, license, policy review workflow 구성 |

## 12. 경쟁/대체 서비스 관점

Topview는 특정 단일 모델과 경쟁한다기보다, 여러 모델과 workflow를 묶는 layer로 경쟁한다.

| 비교 축 | 단일 모델 API | Topview |
|---|---|---|
| 모델 선택 | 특정 provider/model에 고정 | 여러 video/image/audio/avatar 모델 선택 가능 |
| workflow | 입력 -> 결과 위주 | product URL, avatar, board, script, export 등 end-to-end workflow |
| 과금 | provider별 과금 | Topview credit/billing으로 통합 |
| 에셋 관리 | 별도 구현 필요 | Board/Workspace 제공 |
| 커머스 특화 | 직접 구현 필요 | Product Avatar, URL-to-Video, Anyshoot 제공 |
| vendor lock-in | 모델 provider에 종속 | Topview 계정/credit/workflow에 종속 |

따라서 Topview의 경쟁력은 “가장 좋은 단일 영상 모델”이 아니라, **많은 모델과 광고 제작 workflow를 한 제품에서 연결해 production velocity를 높이는 것**에 있다.

## 13. 도입 판단 기준

### 적합한 경우

| 조건 | 이유 |
|---|---|
| 상품/광고 영상 variant가 많이 필요함 | URL-to-Video, Product Avatar, batch/API workflow가 유용 |
| 여러 AI 모델을 비교하며 써야 함 | Board와 통합 API가 vendor switching 비용을 줄임 |
| Avatar/voice/localization이 중요함 | Avatar4, voice clone, TTS, lip sync 기능이 결합됨 |
| 개발팀이 내부 CMS/상품 DB와 연결하려 함 | REST API와 fileId/taskId 구조로 자동화 가능 |
| 팀 단위로 에셋/credit을 관리해야 함 | Team workspace, shared board, credit control 기능과 맞음 |

### 신중해야 하는 경우

| 조건 | 이유 |
|---|---|
| 특정 외부 모델의 원본 API 파라미터를 100% 제어해야 함 | Topview가 추상화한 파라미터만 제공할 수 있음 |
| 결과물을 장기 URL로 바로 제공하려 함 | Topview 결과 URL은 만료될 수 있음 |
| 비용 예측이 매우 엄격함 | 모델별 단가와 native audio/해상도/길이 옵션이 복잡 |
| 데이터 처리 위치/제3자 제공 여부가 엄격한 산업군 | 외부 모델/스토리지/CDN 사용 가능성을 법무/보안에서 검토해야 함 |
| Drama Studio 내부 workflow 전체를 API로 제어해야 함 | 공개 API 범위와 웹 UI 기능 범위가 다를 수 있음 |

## 14. 추천 문서 체계

Topview 관련 문서는 하나로 합치기보다 다음처럼 분리하는 것이 좋다.

| 문서 | 목적 |
|---|---|
| `topview_service_analysis_complete.md` | 서비스 개요, 제품 구조, 업무 적용, 리스크 분석 |
| `topview_api_reference_expanded.md` | endpoint별 request/response 상세 |
| `topview_external_services_investigation.md` | 외부 모델/서비스 의존성 조사 |
| `topview_api_quickstart.md` | 개발자가 바로 연동할 인증/upload/submit/query 예제 |
| `topview_api_model_matrix.md` | 모델별 provider, 지원 파라미터, 단가, 제한사항 |

## 15. 최종 판단

Topview.ai는 “AI 영상 생성 모델 하나”가 아니라 **마케팅/커머스/Avatar/드라마 제작을 위한 AI production workspace**다. 웹 제품은 Video Agent, Canvas, Drama Studio, Board를 통해 사람이 직접 제작하고 조정하는 흐름을 제공하고, API는 같은 생태계를 외부 시스템에서 자동화할 수 있게 한다.

가장 중요한 분석 포인트는 다음 세 가지다.

1. Topview는 자체 기능과 외부 AI 모델을 함께 제공하는 통합 플랫폼이다.
2. API 연동은 `fileId -> submit task -> query task -> result URL/fileId -> 자체 저장` 흐름으로 설계해야 한다.
3. 모델/요금/credit/URL 유효기간/외부 서비스 의존성은 변동 가능성이 높으므로 운영 문서와 설정값으로 관리해야 한다.

## 16. 참고 출처

- Topview 공식 사이트: https://www.topview.ai/
- Topview 공식 API 소개: https://www.topview.ai/openapi
- Topview 가격 페이지: https://www.topview.ai/pricing
- Topview Official Guide: https://www.topview.ai/guides/topview-official-guide
- Topview API Getting Started: https://docs.topview.ai/docs/getting-started
- Topview Billing Rules: https://docs.topview.ai/docs/billing-rules
- Topview Concurrency and Storage: https://docs.topview.ai/docs/concurrency-and-storage
- Topview API Reference: https://docs.topview.ai/reference
- 기존 API 상세 문서: `outputs/topview_api_reference_expanded.md`
- 외부 모델/서비스 조사 문서: `outputs/topview_external_services_investigation.md`
