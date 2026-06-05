> 본 문서는 AI 환각 현상을 줄이기 위한 XAI(설명 가능한 AI) 구현 및 공통 AI 검증 코어 구축에 필요한 오픈소스 생태계를 정리한 시장조사 자료입니다. 모든 도구는 2026년 6월 기준 GitHub 및 HuggingFace를 통해 직접 검증하였습니다.

---

## 1. 전체 구조 이해하기

AI가 답변을 만드는 과정은 아래 4개의 레이어로 나눌 수 있습니다. 각 레이어마다 담당하는 역할이 다르고, 역할에 맞는 오픈소스 도구가 존재합니다.

```
사용자 질문
     ↓
① 재료 준비 레이어    관련 자료를 먼저 찾아서 AI에게 전달
     ↓
② 생산 레이어         AI가 실제로 답변 생성
     ↓
③ 품질 검사 레이어    "이 답변, 근거 있는 말이야?" 확인
     ↓
④ 설명 레이어         "왜 이 답변이 나왔는지" 사람이 이해할 수 있게 표시
                       ← 이것이 XAI (설명 가능한 AI)
```

> **비유:** 카페 주문 시스템처럼 생각하면 쉽습니다. 재료 준비 → 음료 제조 → 품질 검사 → 영수증(설명) 출력

---

## 2. 레이어별 오픈소스 현황

### 레이어 ① 재료 준비 — RAG 구현 도구

#### RAG란 무엇인가?

RAG(Retrieval-Augmented Generation, 검색 증강 생성)는 특정 소프트웨어가 아닌 **설계 방식(방법론)** 입니다.

AI 환각의 주요 원인 중 하나는, AI가 모르는 것을 지어내는 것입니다. RAG는 AI가 답변하기 전에 **관련 문서를 먼저 검색해서 참고하게** 만드는 방식으로 이 문제를 줄입니다.

> **비유:** RAG 없을 때 → 시험에서 기억에만 의존해서 답 쓰는 학생 (틀릴 가능성 높음) / RAG 있을 때 → 교재를 펼쳐보고 찾아서 답 쓰는 학생 (근거 있는 답변)

#### RAG를 구현하는 오픈소스 도구들

아래 세 가지는 **같은 역할을 하는 경쟁 도구**입니다. 하나만 골라 사용하면 됩니다.

|도구|상태|특징|공식 문서|
|---|---|---|---|
|**LangChain**|✅ 활발히 유지보수 중|업계 사실상 표준. 2025년 10월 22일 LangChain 1.0 + LangGraph 1.0 동시 정식 출시. LangChain 1.0은 LangGraph 런타임 위에 구축된 고수준 개발자 인터페이스이며, LangGraph는 저수준 에이전트 런타임으로 함께 사용하는 것이 현재 표준|[docs.langchain.com](https://docs.langchain.com/)|
|LlamaIndex|✅ 활발히 유지보수 중|PDF, DB 등 다양한 데이터 소스 연결에 특화. 최신 릴리즈 2026년 5월|[docs.llamaindex.ai](https://docs.llamaindex.ai/)|
|Haystack|✅ 운영 중|레고처럼 조립 가능한 구조. 상대적으로 덜 사용됨|[docs.haystack.deepset.ai](https://docs.haystack.deepset.ai/)|

> **결론:** LangChain 하나만 선택하면 충분합니다. React가 프론트엔드의 사실상 표준인 것처럼, LangChain은 RAG 분야의 사실상 표준입니다.
>  ※ 복잡한 멀티 에이전트 파이프라인이 필요한 경우 LangGraph(LangChain의 저수준 에이전트 런타임)를 함께 사용합니다.

---

### 레이어 ② 생산 — AI 모델 관련 도구

실제로 답변을 만드는 AI 모델과, 모델을 다루는 도구들입니다.

|도구|상태|설명|공식 문서|
|---|---|---|---|
|**HuggingFace**|✅ 업계 표준|오픈소스 AI 모델들의 앱스토어. 수만 개의 모델이 무료로 공개되어 있음|[huggingface.co/docs](https://huggingface.co/docs)|
|**Ollama**|✅ 활발히 사용 중|내 컴퓨터에서 직접 AI 모델을 실행할 수 있게 해주는 도구|[ollama.com/docs](https://ollama.com/docs)|

> **비유:** HuggingFace는 앱스토어, AI 모델은 앱, Ollama는 그 앱을 내 PC에서 실행하게 해주는 것

---

### 레이어 ③ 품질 검사 — 환각 탐지 도구

AI가 답변을 만들었어요. 근데 그 답변이 진짜 맞는 말인지 어떻게 알죠? 여기서 쓰는 도구들이 "점장" 역할을 합니다. 알바생(AI)이 손님한테 답변을 했을 때, 점장이 옆에서 "야, 그거 맞아?" 확인하는 거예요.

|도구|상태|한 줄 요약|설명|공식 문서|
|---|---|---|---|---|
|**HHEM-2.1-Open** (Vectara)|✅ HuggingFace에서 현재 사용 가능|AI 답변에 "신뢰도 점수"를 붙여주는 도구|오픈소스 환각 탐지 모델의 사실상 표준. AI 답변과 근거 문서를 비교해 0~1 신뢰도 점수 출력. ※ 영어 전용. 상용 버전은 HHEM-2.3이며 다국어(11개 언어) 지원 및 긴 문맥 처리 성능 개선|[HuggingFace 모델 페이지](https://huggingface.co/vectara/hallucination_evaluation_model)|
|**DeepEval**|✅ 활발히 유지보수 중|AI 답변을 자동으로 테스트해주는 도구|프론트엔드에서 코드 배포 전 테스트를 돌리는 것처럼, AI 답변도 배포 전에 자동으로 환각 테스트를 돌릴 수 있음. CI/CD 파이프라인 연동 가능|[docs.confident-ai.com](https://docs.confident-ai.com/)|
|**LM Evaluation Harness**|✅ 활발히 유지보수 중|AI 모델의 환각률을 숫자로 측정하는 도구|"우리 AI가 얼마나 자주 틀리는가?"를 수치로 뽑아줌. HuggingFace 오픈 LLM 리더보드 공식 벤치마크 백엔드. 60개 이상의 학술 벤치마크 지원. ⚠️ 환각을 줄이는 게 아니라 측정하는 도구|[GitHub](https://github.com/EleutherAI/lm-evaluation-harness)|

**HHEM 점수 해석 방법**
```
점수 0.95 이상  →  "이 답변은 신뢰 가능"
점수 0.50 전후  →  "이 답변은 환각 가능성 있음"
점수 0.30 이하  →  "이 답변은 환각일 가능성 높음"
```

**DeepEval 자동 체크 예시**
```
배포 전 자동 체크:
✅ 환각률 5% 이하인가?
✅ 질문과 관련된 답변인가?
✅ 근거 문서 기반으로 답했는가?
→ 통과하면 배포, 실패하면 알림
```

**LM Evaluation Harness 활용 예시**
```
개선 전: 환각률 23%
개선 후: 환각률 8%
→ 15% 개선됐다는 걸 수치로 증명 가능
```

---

### 레이어 ④ 설명 — XAI 도구

AI가 답변도 했고, 점수도 나왔어요. 근데 왜 그 점수가 나왔는지, 어디서 틀렸는지 사람이 눈으로 봐야 하잖아요. 그걸 보여주는 게 XAI 도구입니다.

> **비유:** 병원 검사 결과를 의사가 "이 수치가 높아서 문제입니다" 설명해주는 것처럼, AI 답변도 "이 부분이 근거 없어서 환각입니다" 설명해주는 것

XAI 도구는 크게 두 계보로 나뉩니다.

#### 계보 A — 전통 XAI 도구 (숫자·표 데이터 기반)

언어 AI(챗봇)랑은 직접 맞지 않습니다. 예전에 숫자 데이터 분석할 때 쓰던 도구예요. "이런 것도 있다" 정도로만 알면 됩니다.

> **예시 용도:** 은행에서 "왜 대출이 거절됐나?" → 소득(30%), 신용점수(50%), 나이(20%) 영향

|도구|상태|설명|공식 문서|
|---|---|---|---|
|**SHAP**|✅ 업계 표준|각 입력 요소가 결과에 얼마나 영향을 줬는지 수치로 보여줌. 가장 수학적으로 엄밀함|[shap.readthedocs.io](https://shap.readthedocs.io/)|
|**LIME**|✅ 사용 가능|복잡한 AI 판단을 특정 예측 하나에 대해 단순하게 풀어서 설명|[GitHub](https://github.com/marcotcr/lime)|

#### 계보 B — LLM 특화 XAI·관찰 도구 (현재 트렌드)

챗GPT 같은 언어 AI가 왜 그 답을 했는지 추적하고 보여주는 최신 도구들입니다.

| 도구                | 상태           | 한 줄 요약                              | 설명                                                                                                                    | 공식 문서                                                    |
| ----------------- | ------------ | ----------------------------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Arize Phoenix** | ✅ 매우 활발      | AI 답변 전 과정을 CCTV처럼 녹화해서 보여주는 도구     | 어떤 문서를 검색했는지, 어떤 근거로 답변이 나왔는지, 왜 이 점수인지를 대시보드로 시각화. 자체 호스팅 무료(Elastic License 2.0). ※ OSI 승인 오픈소스 아님, 관리형 SaaS 재판매 제한 | [arize.com/docs/phoenix](https://arize.com/docs/phoenix) |
| **RAGAS**         | ✅ 활발히 유지보수 중 | RAG 시스템이 제대로 작동하는지 점수로 보여주는 도구      | 검색이 관련 문서를 잘 찾았는지(관련성 점수), AI가 찾은 문서를 충실히 반영했는지(충실도 점수)를 측정. RAG 평가 사실상 표준 메트릭                                        | [docs.ragas.io](https://docs.ragas.io/)                  |
| **TruLens**       | ✅ 유지보수 중     | AI 답변이 얼마나 근거 있는지 점수로 보여주는 도구       | TruEra(제작사)의 기술 자산을 Snowflake가 2024년 5월 취득. TruLens 자체는 오픈소스 유지. ※ 인수 이후 성장세 둔화                                       | [trulens.org](https://www.trulens.org/)                  |
| **Langfuse**      | ✅ 매우 활발      | AI 앱 전체를 로그로 기록하고 대시보드로 한눈에 보여주는 도구 | 환각 발생 횟수, 평균 신뢰도, 자주 틀린 질문 유형 등을 모니터링. MIT 라이선스. 2026년 1월 ClickHouse 인수 이후에도 MIT 라이선스 및 자체 호스팅 유지 약속                  | [langfuse.com/docs](https://langfuse.com/docs)           |

---

## 3. 전체 아키텍처 제안

위에서 소개한 도구들을 어떻게 조합하는지 보여주는 구조입니다. 이것이 현재 업계에서 가장 많이 사용되는 패턴입니다.

```
사용자 질문
     ↓
┌──────────────────────────────────────┐
│  LangChain (+ LangGraph)             │
│  - PDF, DB, 웹 등 관련 문서 검색      │
│  - 질문 + 검색된 문서를 AI에 함께 전달 │
└──────────────────────────────────────┘
     ↓
┌──────────────────────────────────────┐
│  AI 모델 (GPT / LLaMA 등)             │
│  - 문서를 참고해서 답변 생성            │
│  - HuggingFace 또는 Ollama로 실행      │
└──────────────────────────────────────┘
     ↓
┌──────────────────────────────────────┐
│  HHEM-2.1-Open                       │
│  - 답변이 근거 문서와 얼마나 일치하는지  │
│  - 신뢰도 점수 0~1 출력               │
│                                      │
│  + DeepEval                          │
│  - 배포 전 환각률 자동 테스트           │
└──────────────────────────────────────┘
     ↓
┌──────────────────────────────────────┐
│  Arize Phoenix  (XAI 핵심)            │
│  - 어떤 문서를 참고했는지 시각화        │
│  - 어느 부분이 근거 없는지 표시         │
│  - 왜 이 점수가 나왔는지 설명           │
│                                      │
│  + RAGAS                             │
│  - RAG 시스템 품질 점수 측정           │
│                                      │
│  + Langfuse                          │
│  - 전체 파이프라인 로그 및 모니터링      │
└──────────────────────────────────────┘
     ↓
사용자에게 답변 + 설명 함께 표시
```

---

## 4. 도구 선택 최종 요약

|레이어|선택 도구|이유|공식 문서|
|---|---|---|---|
|RAG 구현|**LangChain**|업계 표준, v1.0 정식 출시, 자료 풍부|[docs.langchain.com](https://docs.langchain.com/)|
|모델 실행|**HuggingFace + Ollama**|업계 표준 조합|[huggingface.co/docs](https://huggingface.co/docs)|
|환각 탐지|**HHEM-2.1-Open**|오픈소스 환각 탐지 표준 (영어 전용)|[HuggingFace](https://huggingface.co/vectara/hallucination_evaluation_model)|
|테스트 자동화|**DeepEval**|CI/CD 연동, 활발히 유지보수|[docs.confident-ai.com](https://docs.confident-ai.com/)|
|환각률 측정|**LM Eval Harness**|HuggingFace 공식 벤치마크 백엔드|[GitHub](https://github.com/EleutherAI/lm-evaluation-harness)|
|XAI 시각화|**Arize Phoenix**|자체 호스팅 무료, 가장 활발히 개발 중|[arize.com/docs/phoenix](https://arize.com/docs/phoenix)|
|RAG 품질 측정|**RAGAS**|RAG 평가 사실상 표준|[docs.ragas.io](https://docs.ragas.io/)|
|전체 로그·모니터링|**Langfuse**|MIT 라이선스, 자체 호스팅 가능|[langfuse.com/docs](https://langfuse.com/docs)|

> ※ Langfuse는 전체 AI 파이프라인의 로그·모니터링 용도로 폭넓게 사용됩니다. ClickHouse 인수 이후에도 MIT 라이선스와 자체 호스팅을 공식적으로 유지하고 있습니다.

---

## 5. 통합 도구 조사 — 하나로 줄일 수 있는가?

위 도구들을 **하나로 다 커버하는 오픈소스는 2026년 현재 존재하지 않습니다.**
업계에서는 역할별로 전문 도구를 조합하는 것이 현재 표준입니다. 단, 도구 수를 줄이고 싶다면 아래 두 가지가 가장 넓은 범위를 커버합니다.

---

### 통합 옵션 A — Giskard

> **한 줄 요약:** DeepEval + RAGAS(v2 기준) 두 개를 하나로 묶은 테스트 자동화 도구

Giskard는 환각 탐지(HHEM과 같은 신뢰도 점수 산출)를 직접 수행하는 도구가 아닙니다. 시나리오 기반 자동 테스트와 RAG 평가를 하나의 프레임워크에서 처리하는 도구입니다.

```
기존 방식:
DeepEval 설치 → RAGAS 설치 → 각각 따로 연결

Giskard 사용 시 (v2 기준):
Giskard 하나만 설치 → 자동 테스트 + RAG 평가 한 번에 가능
```

보안 취약점, 편향 검사도 하나의 라이브러리에서 처리합니다. DeepLearning.AI와 공동으로 LLM 레드팀 테스트 강좌를 개발할 만큼 신뢰도가 검증된 도구입니다.

**Giskard로 대체 가능한 범위**
```
✅ DeepEval (테스트 자동화)     → Giskard로 대체 가능 (v2 기준)
✅ RAGAS (RAG 품질 측정)       → Giskard의 RAGET 툴킷으로 대체 가능 (v2 기준)
❌ HHEM (환각 탐지 점수 모델)  → 대체 불가. Giskard는 신뢰도 점수를 산출하는
                                  분류 모델이 아닌 테스트 프레임워크
❌ LangChain (RAG 파이프라인)  → 별도로 필요 (단, Giskard와 연동 지원)
❌ Arize Phoenix (XAI 시각화)  → 별도로 필요
❌ Langfuse (전체 로그)        → 별도로 필요
```

**⚠️ 주의사항 — 지금 당장 쓰기엔 시기상조**
Giskard가 v3 버전으로 완전히 새로 만들어지는 중인데, 아직 베타(미완성) 상태입니다. v3에서는 기존 v2의 핵심 기능인 Scan(자동 취약점 탐지)과 RAGET(RAG 평가)이 아직 지원되지 않습니다. ([공식 발표 (2026.04.02)](https://www.giskard.ai/knowledge/announcing-giskard-open-source-v3))

```
v2 → 핵심 기능(Scan, RAGET) 있음, 유지보수 중단 ❌
v3 → 유지보수 활발, 핵심 기능 아직 미완성 ⚠️
```

안정적인 사용을 원한다면 v3 정식 출시 이후 도입을 권장합니다.

|항목|내용|
|---|---|
|GitHub|[Giskard-AI/giskard-oss](https://github.com/Giskard-AI/giskard-oss)|
|라이선스|Apache 2.0 (완전 무료)|
|현재 상태|v3 베타 진행 중|
|공식 문서|[docs.giskard.ai](https://docs.giskard.ai/)|

---

### 통합 옵션 B — Langfuse

> **한 줄 요약:** Arize Phoenix 역할까지 흡수한 모니터링 + XAI 통합 도구

이미 4번 요약표에 포함된 Langfuse가 생각보다 훨씬 넓은 범위를 커버합니다. 로그·모니터링 도구로만 소개했지만, 실제로는 Arize Phoenix가 하는 시각화 기능까지 포함하고 있어요.

```
기존 방식:
Arize Phoenix (시각화) + Langfuse (로그) → 2개 필요

Langfuse만 사용 시:
로그 기록 + 대시보드 시각화 + DeepEval·RAGAS 연동 → 1개로 가능
```

**Langfuse로 대체 가능한 범위**
```
✅ Arize Phoenix (XAI 시각화)  → Langfuse의 트레이싱·대시보드로 대체 가능
✅ RAGAS와 연동               → Langfuse 안에서 점수 붙이기 가능
✅ DeepEval과 연동            → Langfuse 안에서 함께 사용 가능
❌ LangChain (RAG 파이프라인) → 별도로 필요 (단, LangChain과 공식 연동 지원)
❌ HHEM (환각 탐지 모델)      → 별도로 필요
```

**2026년 1월 ClickHouse 인수 이후 변화**
- MIT 라이선스 및 자체 호스팅 공식 유지 약속
- ClickHouse의 고성능 데이터베이스 기반으로 안정성 강화
- Fortune 50 기업 중 19개사, Fortune 500 기업 중 63개사 사용 중

|항목|내용|
|---|---|
|GitHub|[langfuse/langfuse](https://github.com/langfuse/langfuse)|
|라이선스|MIT (자체 호스팅 가능)|
|현재 상태|ClickHouse 인수 후 활발히 개발 중|
|공식 문서|[langfuse.com/docs](https://langfuse.com/docs)|

---

### 도구 수 최소화 시 추천 조합
![[tool_reduction_before_after.svg|495]]
현재 가능한 최소 조합은 아래와 같습니다. 8개 → 5개로 줄일 수 있습니다.

|역할|기존 (8개)|최소 조합 (5개)|
|---|---|---|
|RAG 파이프라인|LangChain|LangChain (대체 불가)|
|모델 실행|HuggingFace + Ollama|동일|
|환각 탐지|HHEM|HHEM (대체 불가 — Giskard는 신뢰도 점수 산출 모델 아님)|
|테스트 + RAG 평가|DeepEval + RAGAS|**Giskard 하나로** (단, v2 기준. 베타 주의)|
|시각화 + 로그 + 모니터링 + 평가|Arize Phoenix + Langfuse|**Langfuse 하나로**|
|환각률 측정|LM Eval Harness|동일|

> ※ Giskard v3 정식 출시 전까지는 기존 조합(DeepEval + RAGAS)을 사용하는 것이 더 안정적입니다. 
> ※ HHEM은 Giskard로 대체되지 않습니다. HHEM은 RAG 출력에 0~1 신뢰도 점수를 붙이는 분류 모델이고, Giskard는 시나리오 기반 테스트 프레임워크로 역할이 다릅니다.

---

## 6. 실제 서비스 도입 사례 — 4개 레이어 구조 기준 검증

> 본 섹션은 4개 레이어(재료 준비 → 생산 → 품질 검사 → 설명) 구조를 실제 프로덕션 서비스에서 구현한 사례를 정리합니다. 각 항목의 기술 정보는 해당 서비스의 공식 페이지 또는 공식 기술 블로그에서 직접 확인한 내용만 기재하였으며, 확인 URL을 함께 명시합니다.

---

### 사례 ① Vectara — RAG-as-a-Service 플랫폼

**서비스 URL:** https://www.vectara.com  
**분류:** 환각 완화 기능을 핵심 가치로 제공하는 API 플랫폼

|레이어|Vectara의 실제 구현|확인 URL|
|---|---|---|
|① 재료 준비 (RAG)|Boomerang 임베딩 모델로 문서 검색 수행|https://www.vectara.com/business/platform/models|
|② 생산 (AI 모델)|Mockingbird LLM — RAG 답변 생성에 최적화된 자체 개발 모델|https://www.vectara.com/business/platform/models|
|③ 품질 검사|HHEM-2.3 — 모든 쿼리 API 응답에 FCS(Factual Consistency Score) 자동 반환|https://huggingface.co/vectara/hallucination_evaluation_model|
|④ 설명 (XAI)|각 답변에 출처 문서 인용 자동 첨부|https://www.vectara.com/business/platform/vectara-for-evaluation|

**레이어 충족:** ① ② ③ ④ 모두 충족

**특이사항:**
- 본 문서 레이어 ③에서 권장하는 HHEM-2.1-Open의 상용 버전(HHEM-2.3)을 직접 개발한 회사가 Vectara입니다. 
- 오픈소스 버전(HHEM-2.1-Open)은 HuggingFace에서 누구나 사용 가능하며, Vectara 플랫폼에서는 다국어(11개 언어) 지원 및 긴 문맥을 처리하는 상용 버전이 자동 적용됩니다.

---

### 사례 ② Glean — 기업용 AI 지식 검색 플랫폼

**서비스 URL:** https://www.glean.com  
**분류:** 사내 문서 전체를 대상으로 환각 완화 구조를 내재화한 엔터프라이즈 AI 검색

|레이어|Glean의 실제 구현|확인 URL|
|---|---|---|
|① 재료 준비 (RAG)|사내 전체 SaaS 문서를 인덱싱 → 쿼리마다 권한 기반 관련 문서 검색|https://www.glean.com/ai-glossary/ai-hallucination|
|② 생산 (AI 모델)|검색된 문서를 컨텍스트로 LLM에 전달해 답변 생성 (RAG 구조)|https://www.glean.com/blog/glean-ai-evaluator|
|③ 품질 검사|AI Evaluator: 답변을 개별 클레임으로 분해 → inferable / generic / ungrounded 3단계로 분류|https://www.glean.com/blog/glean-ai-evaluator|
|④ 설명 (XAI)|라인 단위 인용 — 어느 사내 문서에서 왔는지 출처 표시|https://www.glean.com/ai-glossary/ai-hallucination|

**레이어 충족:** ① ② ③ ④ 모두 충족

**특이사항:** 
- 레이어 ③ 품질 검사의 구체적 구현 방식이 공식 기술 블로그에 상세히 공개된 유일한 사례입니다.
- RAGAS 프레임워크와 유사한 접근 방식을 자체 구현하였으며, AI Evaluator의 human-agreement rate는 74%로 보고되었습니다.
- 출처: https://www.glean.com/blog/glean-ai-evaluator

---

### 사례 ③ Harvey AI — 법률 전문 AI 플랫폼

**서비스 URL:** https://www.harvey.ai  
**분류:** 법률 도메인 특화 환각 완화 시스템을 구축한 버티컬 AI 서비스

|레이어|Harvey의 실제 구현|확인 URL|
|---|---|---|
|① 재료 준비 (RAG)|법령·판례·사용자 업로드 문서 3종을 대상으로 한 RAG 시스템|https://help.harvey.ai/articles/what-ai-models-does-harvey-use|
|② 생산 (AI 모델)|법률 특화 파인튜닝 모델 + o1 추론 모델 조합, 태스크 유형별 모델 라우팅|https://help.harvey.ai/articles/what-ai-models-does-harvey-use|
|③ 품질 검사|답변을 개별 팩트 클레임으로 분해 → 각 클레임을 소스 문서 대비 검증하는 자체 모델 시스템 배포|https://www.harvey.ai/blog/biglaw-bench-hallucinations|
|④ 설명 (XAI)|모든 답변에 판례·법령 출처 인용 첨부|https://help.harvey.ai/articles/what-ai-models-does-harvey-use|

**레이어 충족:** ① ② ③ ④ 모두 충족

**실측 수치 (공식 블로그 직접 확인):** 
- BigLaw Bench 기준 Harvey 모델의 환각률은 0.2% (500개 클레임 중 1개). 비교: Claude 0.7%, Gemini 1.9% 
- 출처: https://www.harvey.ai/blog/biglaw-bench-hallucinations

> ※ 원문에서 직접 확인된 수치는 Harvey 0.2%, Claude 0.7%, Gemini 1.9%입니다. 
> 초안에 기재된 ChatGPT 1.3% 수치는 공식 원문에서 확인되지 않아 삭제하였습니다.

---

### 사례 ④ Perplexity AI — AI 검색 엔진

**서비스 URL:** https://www.perplexity.ai  
**분류:** 환각 완화를 핵심 가치로 내세우는 일반 소비자 대상 AI 검색 서비스

|레이어|Perplexity의 실제 구현|확인 URL|
|---|---|---|
|① 재료 준비 (RAG)|매 쿼리마다 실시간 웹 검색 수행 → 검색 결과를 LLM 컨텍스트에 주입|https://www.perplexity.ai/hub|
|② 생산 (AI 모델)|Sonar 모델 (Llama 3.3 70B 기반 자체 파인튜닝)|https://datanorth.ai/blog/perplexity-ai-what-is-it-and-why-is-it-important|
|③ 품질 검사|검색으로 가져온 문서 외 내용 생성 원칙적 금지 — 정보 부족 시 답변 거부|CEO 직접 발언, https://latenode.com/blog/perplexity-ai|
|④ 설명 (XAI)|모든 답변에 인라인 인용(출처 URL) 의무 표시|https://www.perplexity.ai/hub|

**레이어 충족:** ① ② ③ ④ 모두 충족

**특이사항:** 
- ③ 품질 검사가 별도 평가 모델이 아닌 "검색 문서 외 생성 금지" 원칙으로 구현된 점이 다른 사례와 다릅니다. 
- 레이어 ③을 소프트웨어 도구가 아닌 아키텍처 제약으로 구현한 사례로 참고할 수 있습니다.

---

### 사례 비교 요약

|서비스|① RAG|② 모델|③ 품질검사 방식|④ XAI 설명|도메인|
|---|---|---|---|---|---|
|**Vectara**|✅|✅|HHEM 점수 (0~1 수치)|✅ 인용|범용 API|
|**Glean**|✅|✅|클레임 분해 + 3단계 분류|✅ 인용|기업 내부 지식|
|**Harvey AI**|✅|✅|클레임 분해 + 소스 대비 검증|✅ 인용|법률|
|**Perplexity**|✅|✅|검색 외 생성 금지 원칙|✅ 인용|범용 검색|

> **주의:** 특정 오픈소스 도구(HHEM, DeepEval, RAGAS 등)를 그대로 사용한 사례는 Vectara(HHEM 직접 개발·적용) 외에는 공식적으로 확인되지 않습니다.
