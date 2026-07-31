
- 조사일: 2026-07-30
- 목적: TopView가 노출하는 외부 모델 각각에 대해 **제공사가 공식 API를 실제로 제공하는지**, **유료/무료 여부**를 개별 확인
- 방법: 각 제공사 공식 사이트/개발자 문서 및 신뢰 가능한 3rd-party 가격 비교 자료 교차 확인 (2026-07-30 기준, 가격은 수시 변동 가능)

---

## 1. 비디오 생성 모델

### 1) Veo / Veo 3.1 / Veo 3.2 — Google

- **공식 API**: 있음 — Gemini API / Vertex AI를 통해 공식 제공
- **공식 문서 URL**: https://ai.google.dev/gemini-api/docs/video (Veo 가이드), https://ai.google.dev/gemini-api/docs/veo
- **유/무료**: **유료 전용**. Gemini API 가격표에 Veo용 무료 티어는 없음. 초당 과금 (Veo 3.1 기준 $0.05~$0.40/sec, 해상도·오디오 유무에 따라 차등, 4K는 프리미엄 요율)
- **비고**: Gemini API 자체는 Flash 계열 무료 티어가 있으나, Veo(영상)는 예외적으로 무료 티어 미제공

### 2) Sora 2 — OpenAI

- **공식 API**: 있음 — OpenAI 공식 API (Videos API)
- **공식 문서 URL**: https://platform.openai.com/docs/guides/video-generation
- **유/무료**: **유료 전용**, 무료 티어 없음. Sora 2: $0.10/sec(720p), Sora 2 Pro: $0.30~$0.70/sec(해상도별)
- **⚠️ 리스크**: 소비자용 Sora 앱은 2026-04-26 서비스 종료됨. **API 자체도 2026-09-24 종료(sunset) 예정**으로 확인됨 — TopView가 이 모델을 계속 노출한다면 조만간 대체 모델 전환이 필요할 가능성 높음

### 3) Seedance 1.x/2.x, Seedream — ByteDance

- **공식 API**: 있음 — **BytePlus ModelArk**가 ByteDance의 공식 개발자 플랫폼. 별도로 fal.ai 등도 "공식 파트너"로 재판매
- **공식 문서 URL**: https://docs.byteplus.com/en/docs/ModelArk (개발자 포털: https://www.byteplus.com/en/product/modelark)
- **유/무료**: 신규 가입 시 **무료 체험 제공**(예: 200만 토큰 free trial, Dreamina 콘슈머 앱은 가입 시 무료 크레딧+2회 무료 생성) 이후 **토큰 기반 종량제**로 전환 (720p 기준 $4.3~$7/1M 토큰 등, 모델·해상도별 상이)

### 4) Wan 2.6 / 2.7 — Alibaba

- **공식 API**: 있음 — **Alibaba Cloud Model Studio(DashScope)**, `wan2.7-t2v`, `wan2.7-i2v` 등 공식 API reference·SDK(Python/Java) 제공
- **공식 문서 URL**: https://www.alibabacloud.com/help/en/model-studio/text-to-video-api-reference , 콘솔: https://bailian.console.alibabacloud.com/
- **유/무료**: 국제(싱가포르) 리전 신규 가입자에 한해 **무료 쿼터 제공**(모델당 텍스트 토큰 100만, 이미지 100장, 비디오 약 50초 등), 중국 본토 리전은 무료 쿼터 없음. 소진 후 종량제 전환
- **⚠️ 참고**: Wan 소비자 멤버십(구독)과 Model Studio API 과금은 **완전히 별개 시스템** — 멤버십 크레딧이 API에 적용되지 않음

### 5) Kling / Kling V3 / O1·O3 / Kling 2.6 — Kuaishou

- **공식 API**: 있음 — 개발자 콘솔
- **공식 문서 URL**: https://app.klingai.com/global/dev
- **유/무료**: **API는 유료 전용**. 웹앱(소비자용)은 하루 66 크레딧 무료 티어가 있지만, **개발자 API 크레딧과는 완전히 분리된 시스템**이라 웹앱 무료 크레딧이 API에 적용되지 않음. API는 최소 $9.8~$98 트라이얼 팩 단위 선결제 필요 (엔터프라이즈 팩은 최대 ~$4,200)
- **비고**: 과거(2024년경) Kuaishou가 API를 공식 제공하지 않아 PiAPI 등 비공식 wrapper가 존재했던 시기가 있었으나, 현재는 공식 API로 정착된 상태

### 6) Hailuo / MiniMax-Hailuo-* — MiniMax

- **공식 API**: 있음 — MiniMax Open Platform
- **공식 문서 URL**: https://www.minimax.io/pricing (요금), API 베이스: `https://api.minimax.io/v1`
- **유/무료**: 기본적으로 **Pay-as-you-go 유료**. 일부 모델에 한해 제한적 무료 엔드포인트 존재(예: `Music-3.0-free`, 분당 3회 제한) — 즉 "일부 무료 항목은 있으나 전면 무료 아님"
- **비고**: 별도로 월 구독형 Token Plan($20~$120/월)도 운영

### 7) Vidu Q2/Q3 — Vidu (Shengshu AI 계열)

- **공식 API**: 있음 — 공식 API 플랫폼
- **공식 문서 URL**: https://platform.vidu.com/ (가격: https://platform.vidu.com/docs/pricing)
- **유/무료**: **크레딧 기반 종량제**($0.005/credit) 유료가 기본. 간헐적 프로모션(신규가입 무료 포인트, 충전 보너스)은 있으나 상시 무료 티어는 아님
- **⚠️ 주의**: `vidu.io`라는 이름의 별개 세일즈용 개인화 영상 서비스가 존재 — TopView 문서상 "Vidu Studio"는 `platform.vidu.com`/`vidu.com` 쪽으로 판단됨. 문서상 회사명 특정 시 혼동 주의

### 8) Runway

- **공식 API**: 있음
- **공식 문서 URL**: https://docs.dev.runwayml.com/ (개발자 포털: https://runwayml.com/developers)
- **유/무료**: **API는 유료 전용** (최소 $10 크레딧 충전 필요, $0.01/credit). 소비자 웹앱(Standard 이상 구독)에는 무료 티어(125 크레딧)가 있으나 API와는 별도 시스템

### 9) Grok Video / Grok Video Edit / Grok Video Extend — xAI

- **공식 API**: 있음 — xAI 공식 API에 `grok-imagine-video`, `grok-imagine-image` 등으로 명시적으로 존재 (기존 조사서의 "중간 신뢰도" 대비 상향 확인됨)
- **공식 문서 URL**: https://docs.x.ai/developers/model-capabilities/imagine (API 엔드포인트: `https://api.x.ai/v1/videos/generations`)
- **유/무료**: **유료 전용** 초당 과금 (약 $0.05/sec 수준, 이미지는 $0.02~$0.07/image). xAI API 전체에는 데이터 공유 프로그램 참여 시 월 최대 $175 상당의 API 크레딧 제공 프로그램이 있어 이를 통해 부분적으로 무료 활용 가능
- **비고**: 문서상 "Grok Video"가 정확히 `grok-imagine-video`(speed 빌드)인지 `grok-imagine-1.5-video`(quality 빌드)인지는 TopView 쪽 문서만으로는 특정 불가

### 10) Gemini Omni Flash — Google (Gemini 계열, 확인됨)

- **공식 API**: 있음 — Google 공식 문서에 "Gemini Omni Flash"가 **Veo 3.1과 나란히 명시된 공식 Gemini API 비디오 모델**로 직접 확인됨 (빠른 대화형 비디오 편집/생성용)
- **공식 문서 URL**: https://ai.google.dev/gemini-api/docs/video
- **유/무료**: Veo와 마찬가지로 **유료 전용**으로 추정 (Google 비디오 계열 모델은 무료 티어 없음)
- **비고**: 기존 조사서의 "Google Gemini 계열로 추정" → 이번 조사에서 Google 공식 문서(Video generation in the Gemini API 페이지)에 명시적으로 등재된 것을 확인, 신뢰도 "높음"으로 상향

---

## 2. 이미지 생성 모델

### 11) GPT Image 2 — OpenAI

- **공식 API**: 있음 — OpenAI 공식 Image API
- **공식 문서 URL**: https://platform.openai.com/docs/guides/image-generation
- **유/무료**: **유료 전용**, 무료 티어 없음. 해상도·품질별 $0.005~$0.211/image. (신규 가입 시 OpenAI 전체 API에 적용되는 $5 크레딧은 있음)

### 12) Flux — Black Forest Labs

- **공식 API**: 있음
- **공식 문서 URL**: https://docs.bfl.ml/ (가격: https://bfl.ai/pricing)
- **유/무료**: **API는 무료 티어 없음** (크레딧제, 1 credit=$0.01, 이미지당 과금). 단, 오픈소스 가중치 모델(FLUX.1 [schnell], [dev])은 자체 호스팅 시 무료로 사용 가능 — "API 자체는 유료, 오픈웨이트 셀프호스팅은 무료"로 구분 필요

### 13) Nano Banana / Nano Banana Pro — Google (신원 확인됨)

- **공식 API**: 있음 — **Google Gemini 3.1 Flash Image 모델의 코드네임**으로 확인됨 (Gemini API 공식 이미지 생성 모델)
- **공식 문서 URL**: https://ai.google.dev/gemini-api/docs/image-generation
- **유/무료**: **유료 전용**, 해상도별 $0.045~$0.151/image
- **비고**: 기존 조사서의 "제공사명 미확인, 외부/제휴 모델 가능성" → **Google 자체 모델로 특정 가능**하게 됨

### 14) Ideogram

- **공식 API**: 있음
- **공식 문서 URL**: https://ideogram.ai/api-pricing/
- **유/무료**: **API 자체는 무료 티어 없음**, $0.025~$0.10/image 종량제. (API 계정과 구독 결제 시스템은 별개). 단 소비자 웹앱은 주 10크레딧 무료 제공

---

## 3. AI 음악

### 15) Minimax Music 2.6 — MiniMax

- **공식 API**: 있음 — MiniMax 공식 플랫폼 내 Music API
- **공식 문서 URL**: https://www.minimax.io/pricing
- **유/무료**: 기본 **유료**(트랙당 $0.03~$0.15 수준). 단, `Music-3.0-free`/`Music-2.6-free`처럼 **제한적 무료 엔드포인트**(분당 3회 제한)가 공식적으로 존재

---

## 4. 음성/TTS

### 16) ElevenLabs V2.5 / V3

- **공식 API**: 있음 — 업계 표준급 공식 API
- **공식 문서 URL**: https://elevenlabs.io/pricing/api (문서: https://elevenlabs.io/docs)
- **유/무료**: **무료 티어 있음** — 월 10,000 크레딧(약 TTS 10분), 단 **상업적 이용 불가**(무료 티어 콘텐츠는 비상업 용도로 제한, ElevenLabs 표기 의무). 상업 이용은 Starter($6/월)부터

### 17) minimax-v2.5 (Voice) — MiniMax

- **공식 API**: 있음 (위 MiniMax 항목과 동일 플랫폼)
- **공식 문서 URL**: https://www.minimax.io/pricing
- **유/무료**: 기본 유료 종량제(Speech: 100만 자당 $60 등), 별도 무료 티어는 확인 안 됨

---

## 5. 음성 복제

### 18) Fish Audio S2 Pro

- **공식 API**: 있음
- **공식 문서 URL**: https://docs.fish.audio/ (가격: https://docs.fish.audio/developer-guide/models-pricing/pricing-and-rate-limits)
- **유/무료**: **무료 티어 있음** — 월 8,000 크레딧(약 7분 분량), 기본 보이스 클로닝 포함하되 **비상업적 용도로 제한**. 상업 이용은 Plus($11~20/월)부터. API는 Pay-as-you-go

### 19) Index TTS — Bilibili(빌리빌리) Index팀

- **공식 API**: **⚠️ 없음(중요 차이점)**. IndexTTS(1.0/1.5/2.0)는 Bilibili가 **오픈소스로 공개한 모델**(GitHub/HuggingFace 가중치 배포)일 뿐, **Bilibili 자체가 호스팅하는 공식 상용 API는 확인되지 않음**
- **오픈소스 저장소 URL(공식 API 아님)**: https://github.com/index-tts/index-tts , https://huggingface.co/IndexTeam/IndexTTS-2
- **3rd-party 유료 호스팅 예시(비공식)**: https://302.ai/product/detail/2429
- **유/무료**: 원 모델 자체는 오픈웨이트라 **셀프호스팅 시 무료**. 다만 시중에서 "IndexTTS API"라고 불리는 것은 대부분 **302.AI 등 3rd-party가 오픈소스 가중치를 가져와 호스팅한 유료 API**($15/1M 토큰 등)이며, Bilibili 공식 API가 아님
- **⚠️ 리스크 포인트**: 원 조사서에 "중간~높음" 신뢰도로 기재되어 있었으나, 실제로는 **"공식 API 없음, 오픈소스 모델을 제3자가 대행 호스팅"** 구조로 재분류 필요. TopView가 이를 사용 중이라면 Bilibili와 직접 계약 관계가 아니라 중간 호스팅사(예: 302.AI 등)를 경유할 가능성이 높음 → 데이터 처리 주체 리스크 재검토 권장

---

## 6. 종합 정리표

|#|모델|제공사|공식 API|무료 티어|공식 문서/사이트 URL|
|---|---|---|---|---|---|
|1|Veo 3.1/3.2|Google|✅|❌ 유료전용|https://ai.google.dev/gemini-api/docs/video|
|2|Sora 2|OpenAI|✅|❌ 유료전용|https://platform.openai.com/docs/guides/video-generation (**2026-09-24 종료 예정**)|
|3|Seedance/Seedream|ByteDance|✅|🔶 가입시 무료체험|https://docs.byteplus.com/en/docs/ModelArk|
|4|Wan 2.6/2.7|Alibaba|✅|🔶 국제리전 신규가입 무료쿼터|https://www.alibabacloud.com/help/en/model-studio/text-to-video-api-reference|
|5|Kling|Kuaishou|✅|❌ API는 유료전용|https://app.klingai.com/global/dev|
|6|Hailuo/MiniMax|MiniMax|✅|🔶 일부 free 엔드포인트만|https://www.minimax.io/pricing|
|7|Vidu Q2/Q3|Vidu|✅|🔶 프로모션성 무료포인트만|https://platform.vidu.com/|
|8|Runway|Runway|✅|❌ API는 유료전용|https://docs.dev.runwayml.com/|
|9|Grok Video|xAI|✅|🔶 API 크레딧 프로그램 경유시|https://docs.x.ai/developers/model-capabilities/imagine|
|10|Gemini Omni Flash|Google|✅ (확인됨)|❌ 유료전용(추정)|https://ai.google.dev/gemini-api/docs/video|
|11|GPT Image 2|OpenAI|✅|❌ 유료전용|https://platform.openai.com/docs/guides/image-generation|
|12|Flux|Black Forest Labs|✅|🔶 오픈웨이트만 무료|https://docs.bfl.ml/|
|13|Nano Banana(Pro)|**Google** (신원확인)|✅|❌ 유료전용|https://ai.google.dev/gemini-api/docs/image-generation|
|14|Ideogram|Ideogram|✅|🔶 웹앱만 무료|https://ideogram.ai/api-pricing/|
|15|Minimax Music 2.6|MiniMax|✅|🔶 일부 free 엔드포인트만|https://www.minimax.io/pricing|
|16|ElevenLabs V2.5/V3|ElevenLabs|✅|✅ 무료티어(비상업)|https://elevenlabs.io/pricing/api|
|17|minimax-v2.5(voice)|MiniMax|✅|❌|https://www.minimax.io/pricing|
|18|Fish Audio S2 Pro|Fish Audio|✅|✅ 무료티어(비상업)|https://docs.fish.audio/|
|19|Index TTS|**Bilibili(오픈소스)**|❌ **공식 API 없음**|✅(셀프호스팅시)|https://github.com/index-tts/index-tts (오픈소스 저장소, 공식 API 아님)|

범례: ✅ 있음 / ❌ 없음 / 🔶 조건부·제한적

---

## 7. 원 조사서 대비 업데이트가 필요한 포인트

1. **Nano Banana/Nano Banana Pro**: "제공사명 미확인" → **Google(Gemini 3.1 Flash Image)**로 특정 가능
2. **Index TTS**: "중간~높음 신뢰도의 외부 API"로 분류돼 있었으나, 실제로는 **Bilibili의 공식 상용 API가 존재하지 않는 오픈소스 모델**이며, TopView가 이를 쓴다면 필연적으로 제3자 호스팅사를 경유 — 리스크 등급 상향 검토 필요
3. **Sora 2**: 공식 API 자체가 **2026-09-24 종료(sunset) 예정** — TopView 리스크 테이블의 "모델명/버전 변경" 항목에 구체적 시한으로 반영 권장
4. **Kling/Wan/Runway 공통 패턴**: 소비자용 구독(멤버십)과 개발자 API 과금이 **완전히 분리된 별도 시스템**인 경우가 많음 — TopView의 "과금 방식 차이" 리스크 항목에 "제공사별 소비자 플랜 vs API 플랜 이중구조" 문구 추가 권장
5. **무료 티어 보유 여부**: ElevenLabs, Fish Audio 정도만 실질적인 상시 무료 티어(단, 비상업 조건부)를 제공. 나머지 비디오 계열 모델(Veo/Sora/Kling/Runway 등)은 API 기준으로는 **사실상 전부 유료 전용**이며, "무료체험/프로모션 크레딧"과 "상시 무료 티어"를 혼동하지 않도록 구분 필요