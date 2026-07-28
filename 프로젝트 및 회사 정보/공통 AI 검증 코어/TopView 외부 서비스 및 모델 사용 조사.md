
조사일: 2026-07-28

## 결론 요약

Topview는 온전히 Topview 내부 기능만으로 동작하는 단일 모델 서비스라기보다, **Topview 자체 제작 워크플로우 + 외부 AI 모델/인프라를 묶은 통합 제작 플랫폼**에 가깝다.

공식 사이트는 Topview API를 “하나의 REST endpoint로 Veo, Sora, Seedance, Wan, Kling, Higgsfield, Flux 및 Topview 자체 Avatar/URL-to-Video/Product Avatar/Anyshoot 모델을 호출하는 API”라고 설명한다. 즉, 외부 모델을 감싸는 gateway/API aggregator 성격이 공식 문구에 직접 드러난다.

다만 “Topview가 실제 backend에서 해당 원제공사의 API를 직접 호출한다”까지는 공개 문서만으로 100% 확정할 수 없다. 공개적으로 확인 가능한 것은 **Topview가 해당 모델명을 사용자/API에 노출하고, 같은 계정/과금/Board 안에서 실행 가능하게 제공한다**는 점이다.

## 확실히 외부 모델로 봐야 하는 영역

| 영역 | Topview에 노출된 모델/서비스 | 외부 제공사/출처 | 근거 수준 | 비고 |
|---|---|---|---|---|
| 비디오 생성 | `Veo`, `Veo 3.1`, `Veo 3.2` | Google | 높음 | Topview 공식 API 페이지가 Veo를 Google의 video model로 설명 |
| 비디오 생성 | `Sora 2` | OpenAI | 높음 | Topview 공식 API 페이지가 Sora를 OpenAI 모델로 설명 |
| 비디오 생성 | `Seedance 1.x/2.x`, `Seedream` | ByteDance | 높음 | Topview 공식 모델 페이지와 비교 문서에서 ByteDance 표기 |
| 비디오 생성 | `Wan 2.6`, `Wan 2.7` | Alibaba 계열 모델 | 높음 | Topview 공식 API 페이지가 Wan을 Alibaba 모델로 설명 |
| 비디오 생성 | `Kling`, `Kling V3`, `Kling O1/O3`, `Kling 2.6` | Kuaishou / Kling AI | 높음 | Topview 공식 API 페이지가 Kling을 Kuaishou 모델로 설명 |
| 비디오 생성 | `Hailuo`, `MiniMax-Hailuo-*` | MiniMax | 높음 | Topview 모델 페이지와 API 문서에 Hailuo/MiniMax 모델명 노출 |
| 비디오 생성 | `Vidu Q2/Q3` | Vidu Studio | 높음 | Topview 모델 페이지에 Vidu Studio 표기 |
| 비디오 생성 | `Runway` | Runway | 중간~높음 | Topview 모델 페이지에 Runway AI Video Generator 노출 |
| 비디오 생성/편집 | `Grok Video`, `Grok Video Edit`, `Grok Video Extend` | xAI 계열로 추정 | 중간 | Topview API 문서에는 모델명이 노출되지만 제공사명을 직접 명시하지는 않음 |
| 비디오 생성/편집 | `Gemini Omni Flash` | Google Gemini 계열로 추정 | 중간~높음 | Topview API 문서에서 `Topview Omni`가 폐기되고 `Gemini Omni Flash`를 사용하라고 안내 |
| 이미지 생성 | `GPT Image 2` | OpenAI | 높음 | Topview 공식 API 페이지가 GPT Image를 OpenAI image model로 설명 |
| 이미지 생성 | `Flux` | Black Forest Labs | 높음 | Topview 공식 API 페이지가 Flux를 Black Forest Labs 모델로 설명 |
| 이미지 생성 | `Nano Banana`, `Nano Banana Pro` | 외부/제휴 모델 가능성 높음 | 중간 | Topview 공식 문서에 모델명은 노출되지만 제공사명은 명시되지 않음 |
| 이미지 생성 | `Ideogram` | Ideogram | 중간~높음 | Topview 공식 API 페이지에 이미지 모델로 노출 |
| AI 음악 | `Minimax Music 2.6` | MiniMax | 높음 | AI Music API 문서의 허용 모델값에 포함 |
| 음성/TTS | `ElevenLabs V2.5`, `ElevenLabs V3` | ElevenLabs | 높음 | Photo Avatar API의 `voiceModel` enum에 포함 |
| 음성/TTS | `minimax-v2.5` | MiniMax | 높음 | Photo Avatar API의 `voiceModel` enum에 포함 |
| 음성 복제 | `Fish Audio S2 Pro` | Fish Audio | 높음 | Instant Voice Clone API의 허용 모델값에 포함 |
| 음성 복제 | `Index TTS` | Index TTS | 중간~높음 | Instant Voice Clone API의 허용 모델값에 포함 |

## Topview 자체 기능으로 분리해야 하는 영역

Topview 공식 API 페이지는 외부 모델과 별도로 다음을 “Topview Native APIs”로 분리한다.

| Topview 자체/네이티브로 보이는 기능 | 설명 |
|---|---|
| `Video Avatar` | 광고/마케팅용 talking avatar 생성 |
| `Product Avatar` | 실제 상품을 들고 시연하는 avatar workflow |
| `URL-to-Video` | 상품 URL에서 정보/이미지/카피를 추출해 영상 생성 |
| `Product Anyshoot` | 상품을 lifestyle scene 또는 virtual try-on 형태로 배치 |
| `Topview Pro`, `Topview Plus`, `Topview Best` | Common Task API의 Topview Series 모델 |

이 영역도 내부적으로는 외부 모델을 일부 조합할 수 있지만, 공개 문서상으로는 Topview가 자체 상품/워크플로우로 포장해 제공하는 기능으로 보는 것이 안전하다.

## 인프라/플랫폼 외부 의존성 단서

| 외부 서비스/인프라 | 근거 | 해석 |
|---|---|---|
| AWS S3 | Upload 관련 문서와 Product Avatar 예시 URL에 `S3`, `aigc.s3.amazonaws.com`, S3 path 표현이 반복적으로 등장 | 업로드 파일과 생성 결과 저장소로 S3를 사용하는 것으로 보임 |
| AWS CloudFront | 여러 Query API의 `needCloudFrontUrl` 설명에 CloudFront URL 반환 옵션이 있음 | 결과물 다운로드/가속 CDN으로 CloudFront를 사용하는 것으로 보임 |
| YouTube API Services | Topview Terms에 일부 기능이 YouTube API Services와 통합된다고 명시 | YouTube 관련 가져오기/분석/연동 기능이 있을 가능성 |
| 결제용 third-party payment service | Terms의 Payment 항목에서 결제 처리를 위해 third-party services를 사용할 수 있다고 명시 | Stripe/Paddle 등 구체 사업자는 공개 문서에서 확인되지 않음 |

## 우선 확인해야 할 리스크

| 리스크 | 이유 | 권장 대응 |
|---|---|---|
| 모델명/버전 변경 | Topview 문서에서 `Topview Omni`가 폐기되고 `Gemini Omni Flash`로 대체된 사례가 있음 | 모델명은 설정 파일/DB에서 관리 |
| 모델별 파라미터 제약 차이 | Kling, Veo, Seedance, Wan 등은 duration/resolution/sound/reference input 제한이 다름 | `model`별 validation table 유지 |
| 과금 방식 차이 | Native audio surcharge, 모델별 초당 과금, special billing이 다름 | 사전 견적 계산 로직과 query 결과의 `costCredit` 비교 |
| 외부 모델 장애/혼잡 | 외부 모델을 묶어 제공하는 구조라 특정 모델만 실패/지연될 수 있음 | 모델별 fallback, retry, timeout 정책 필요 |
| 데이터 처리/권리 문제 | 외부 모델 또는 CDN/스토리지 경유 가능성이 있으므로 인물/음성/상품 이미지 권리 이슈가 중요 | Terms, privacy, enterprise DPA, consent workflow 확인 |

## 참고한 주요 공식 출처

- Topview API: https://www.topview.ai/openapi
- Topview Models: https://www.topview.ai/models
- Common Task API Usage: https://docs.topview.ai/reference/image-to-video-v2-text-to-video-omni-reference-api-usage
- Storyboard Submit Task: https://docs.topview.ai/reference/storyboard-submit-task
- AI Music Submit Task: https://docs.topview.ai/reference/ai-music-submit-task
- Instant Voice Clone Submit Task: https://docs.topview.ai/reference/submitinstantvoiceclonetasken
- Photo Avatar Submit Task: https://docs.topview.ai/reference/avatar4_submit_task
- Upload / S3 관련 문서: https://docs.topview.ai/reference/get_upload_credential
