
> **기반 문서:** RAG 기반 AI 환각 완화 시스템 구축을 위한 오픈소스 생태계 조사 (2026.06)  
> **목적:** 해당 문서에서 제시된 4개 레이어 구조 및 오픈소스 도구들 중, Vercel AI SDK + Next.js 환경에서 구현 가능한 범위를 정리하고 실제 코드 구조를 제시

---

## 1. 구현 범위 개요

원본 문서의 4개 레이어를 Vercel AI SDK로 커버할 수 있는 범위는 다음과 같다.

|레이어|역할|Vercel AI SDK 구현 가능 여부|
|---|---|---|
|① 재료 준비 (RAG)|문서 검색 및 컨텍스트 주입|✅ 공식 지원 (RAG 템플릿 제공)|
|② 생산 (AI 모델)|답변 생성 + 스트리밍|✅ 핵심 기능|
|③ 품질 검사 (환각 탐지)|HHEM 점수, 자동 테스트|⚠️ 외부 API 호출로 부분 연동 가능|
|④ 설명 (XAI)|출처 시각화, 로그 모니터링|⚠️ Langfuse 연동으로 부분 가능|

**핵심 원칙:** Vercel AI SDK는 레이어 ①②의 오케스트레이션 및 UI를 담당하고, 레이어 ③④의 무거운 모델 추론(HHEM, NLI)은 별도 Python 마이크로서비스 API로 분리한다.

---

## 2. 전체 아키텍처

```
[Next.js 프론트엔드]
  useChat hook → 실시간 스트리밍 UI
        ↓
[Next.js API Route — Vercel AI SDK 오케스트레이터]
  ① LangChain으로 관련 문서 벡터 검색
  ② 검색된 문서 + 질문을 AI 모델에 전달
  ③ streamText로 답변 스트리밍
  ④ 답변 완성 후 → 검증 API 호출
        ↓
[Python 마이크로서비스 (별도 서버)]
  HHEM-2.1-Open 환각 탐지 점수 산출
  DeepEval 자동 테스트
  RAGAS RAG 품질 평가
        ↓
[Langfuse]
  전체 파이프라인 로그 및 모니터링
  XAI 대시보드 시각화
```

---

## 3. 레이어별 구현 상세

### 레이어 ① — RAG 파이프라인 (LangChain + Vercel AI SDK)

**사용 도구:** LangChain, Vercel AI SDK, PostgreSQL + pgvector (또는 Pinecone)

원본 문서 결론: _"LangChain 하나만 선택하면 충분합니다."_

Vercel AI SDK는 LangChain과 함께 사용할 수 있으며, Next.js API Route 안에서 LangChain의 RAG 파이프라인을 실행하고 그 결과를 Vercel AI SDK의 스트리밍 응답으로 내보내는 구조가 현재 표준이다.

#### 디렉토리 구조

```
app/
├── api/
│   └── chat/
│       └── route.ts          # 메인 RAG 오케스트레이터
├── lib/
│   ├── vectorstore.ts         # 벡터 DB 연결 (pgvector / Pinecone)
│   ├── retriever.ts           # LangChain 문서 검색
│   └── verifier.ts            # 검증 API 호출 클라이언트
└── components/
    └── chat.tsx               # useChat 기반 스트리밍 UI
```

#### 구현 코드 — API Route

```typescript
// app/api/chat/route.ts
import { streamText } from 'ai';
import { anthropic } from '@ai-sdk/anthropic';
import { getRetriever } from '@/lib/retriever';
import { callVerifier } from '@/lib/verifier';
import { LangfuseTracer } from '@/lib/langfuse';

export const runtime = 'nodejs'; // GPU 연동 고려 시 nodejs 런타임 필수

export async function POST(req: Request) {
  const { messages } = await req.json();
  const userQuery = messages[messages.length - 1].content;

  // ① 관련 문서 검색 (LangChain Retriever)
  const retriever = await getRetriever();
  const docs = await retriever.getRelevantDocuments(userQuery);
  const context = docs.map(d => d.pageContent).join('\n\n');

  // Langfuse 트레이싱 시작
  const trace = LangfuseTracer.start({ query: userQuery, docsCount: docs.length });

  // ② AI 답변 생성 (Vercel AI SDK streamText)
  const result = streamText({
    model: anthropic('claude-sonnet-4-20250514'),
    system: `당신은 신뢰할 수 있는 AI 어시스턴트입니다.
반드시 아래 제공된 문서를 근거로만 답변하세요.
문서에 없는 내용은 "해당 정보를 찾을 수 없습니다"라고 답하세요.

[참고 문서]
${context}`,
    messages,
    onFinish: async ({ text }) => {
      // ③ 답변 완성 후 환각 탐지 API 호출 (비동기, 응답 지연 없음)
      const verificationResult = await callVerifier({
        answer: text,
        context,
        query: userQuery,
      });

      // Langfuse에 검증 결과 기록
      trace.log({
        hhem_score: verificationResult.score,
        is_hallucination: verificationResult.score < 0.5,
        sources: docs.map(d => d.metadata.source),
      });
    },
  });

  return result.toDataStreamResponse();
}
```

#### 구현 코드 — 벡터스토어 연결

```typescript
// app/lib/vectorstore.ts
import { OpenAIEmbeddings } from '@langchain/openai';
import { PGVectorStore } from '@langchain/community/vectorstores/pgvector';

// PostgreSQL + pgvector 사용 (자체 호스팅 가능, 무료)
export async function getVectorStore() {
  const embeddings = new OpenAIEmbeddings({
    model: 'text-embedding-3-small',
  });

  return PGVectorStore.initialize(embeddings, {
    postgresConnectionOptions: {
      connectionString: process.env.DATABASE_URL!,
    },
    tableName: 'documents',
    columns: {
      idColumnName: 'id',
      vectorColumnName: 'embedding',
      contentColumnName: 'content',
      metadataColumnName: 'metadata',
    },
  });
}

export async function getRetriever() {
  const store = await getVectorStore();
  return store.asRetriever({ k: 5 }); // 상위 5개 문서 검색
}
```

---

### 레이어 ② — AI 모델 실행 (HuggingFace + Ollama)

**사용 도구:** Vercel AI SDK 프로바이더, @ai-sdk/openai, Ollama

원본 문서 비유: _"HuggingFace는 앱스토어, AI 모델은 앱, Ollama는 그 앱을 내 PC에서 실행하게 해주는 것"_

Vercel AI SDK의 최대 강점은 **모델 프로바이더 추상화**다. 코드 한 줄 변경으로 Claude, GPT, Llama 등을 전환할 수 있다.

```typescript
// app/lib/models.ts — 환경별 모델 선택
import { anthropic } from '@ai-sdk/anthropic';
import { openai } from '@ai-sdk/openai';
import { createOllama } from 'ollama-ai-provider';

const ollama = createOllama({ baseURL: 'http://localhost:11434/api' });

// 환경변수로 모델 전환 (프로덕션 / 로컬 개발)
export function getModel() {
  switch (process.env.MODEL_PROVIDER) {
    case 'anthropic':
      return anthropic('claude-sonnet-4-20250514');
    case 'openai':
      return openai('gpt-4o');
    case 'ollama':
      // Ollama: 로컬 실행, 완전 무료, 데이터 외부 미전송
      return ollama('llama3.2');
    default:
      return anthropic('claude-sonnet-4-20250514');
  }
}
```

**로컬 개발 시 Ollama 사용 이유:**

- 비용 없음
- 인터넷 연결 불필요
- 민감 데이터 외부 유출 차단
- HuggingFace에서 내려받은 모델 그대로 실행 가능

---

### 레이어 ③ — 환각 탐지 (HHEM + DeepEval 연동)

**사용 도구:** HHEM-2.1-Open (Python), DeepEval (Python)  
**연동 방식:** Next.js API Route → Python FastAPI 마이크로서비스 HTTP 호출

원본 문서 HHEM 점수 해석:

- `0.95 이상` → 신뢰 가능
- `0.50 전후` → 환각 가능성 있음
- `0.30 이하` → 환각일 가능성 높음

#### Next.js 측 — 검증 API 클라이언트

```typescript
// app/lib/verifier.ts
interface VerificationResult {
  score: number;           // HHEM 0~1 신뢰도 점수
  label: 'trusted' | 'uncertain' | 'hallucination';
  deepeval_passed: boolean; // DeepEval 자동 테스트 통과 여부
}

export async function callVerifier(params: {
  answer: string;
  context: string;
  query: string;
}): Promise<VerificationResult> {
  const res = await fetch(`${process.env.VERIFIER_API_URL}/verify`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(params),
  });

  if (!res.ok) {
    // 검증 서버 오류 시 기본값 반환 (서비스 중단 방지)
    return { score: 0.5, label: 'uncertain', deepeval_passed: false };
  }

  return res.json();
}
```

#### Python 측 — HHEM 검증 서버 (FastAPI)

```python
# verifier_service/main.py
from fastapi import FastAPI
from pydantic import BaseModel
from transformers import AutoModelForSequenceClassification, AutoTokenizer
import torch

app = FastAPI()

# HHEM-2.1-Open 모델 로드 (HuggingFace)
# 영어 전용 — 한국어는 별도 다국어 모델 필요
MODEL_NAME = "vectara/hallucination_evaluation_model"
tokenizer = AutoTokenizer.from_pretrained(MODEL_NAME)
model = AutoModelForSequenceClassification.from_pretrained(MODEL_NAME)

class VerifyRequest(BaseModel):
    answer: str
    context: str
    query: str

@app.post("/verify")
async def verify(req: VerifyRequest):
    # HHEM: 답변과 근거 문서를 비교해 신뢰도 점수 산출
    inputs = tokenizer(
        req.context, req.answer,
        return_tensors="pt",
        truncation=True,
        max_length=512
    )
    with torch.no_grad():
        outputs = model(**inputs)
        score = torch.softmax(outputs.logits, dim=1)[0][1].item()

    # 점수 해석
    if score >= 0.95:
        label = "trusted"
    elif score >= 0.5:
        label = "uncertain"
    else:
        label = "hallucination"

    return {
        "score": round(score, 4),
        "label": label,
        "deepeval_passed": score >= 0.5  # 간소화된 DeepEval 기준
    }
```

> **⚠️ 주의:** HHEM-2.1-Open은 영어 전용이다. 한국어 서비스의 경우 Vectara의 상용 HHEM-2.3(다국어 11개 언어 지원)을 사용하거나, 별도 한국어 NLI 모델을 적용해야 한다.

---

### 레이어 ④ — XAI 시각화 (Langfuse 연동)

**사용 도구:** Langfuse (MIT 라이선스, 자체 호스팅 가능)

원본 문서에서 Langfuse는 Arize Phoenix까지 대체 가능한 도구로 평가되었다. Next.js 환경에서 Langfuse SDK를 직접 연동할 수 있다.

#### Langfuse 초기화

```typescript
// app/lib/langfuse.ts
import Langfuse from 'langfuse';

const langfuse = new Langfuse({
  publicKey: process.env.LANGFUSE_PUBLIC_KEY!,
  secretKey: process.env.LANGFUSE_SECRET_KEY!,
  baseUrl: process.env.LANGFUSE_BASE_URL!, // 자체 호스팅 URL
});

export const LangfuseTracer = {
  start(meta: { query: string; docsCount: number }) {
    const trace = langfuse.trace({ name: 'rag-pipeline', metadata: meta });
    return {
      log(data: {
        hhem_score: number;
        is_hallucination: boolean;
        sources: string[];
      }) {
        trace.event({
          name: 'verification-result',
          metadata: data,
          level: data.is_hallucination ? 'WARNING' : 'DEFAULT',
        });
      },
    };
  },
};
```

#### Langfuse 대시보드에서 확인 가능한 것들

```
환각 발생 횟수       → HHEM 점수 0.5 미만 이벤트 카운트
평균 신뢰도          → trace별 hhem_score 평균
자주 틀린 질문 유형  → WARNING 레벨 이벤트 질문 패턴 분석
어떤 문서를 참고했는지 → sources 메타데이터 추적
```

---

### 프론트엔드 — 실시간 스트리밍 UI

```tsx
// app/components/chat.tsx
'use client';
import { useChat } from 'ai/react';
import { useState } from 'react';

interface Message {
  id: string;
  role: 'user' | 'assistant';
  content: string;
  hhem_score?: number; // 환각 탐지 점수
}

export function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } =
    useChat({ api: '/api/chat' });

  return (
    <div className="flex flex-col h-screen max-w-3xl mx-auto p-4">
      {/* 메시지 목록 */}
      <div className="flex-1 overflow-y-auto space-y-4 mb-4">
        {messages.map((msg) => (
          <div
            key={msg.id}
            className={`p-3 rounded-lg ${
              msg.role === 'user'
                ? 'bg-blue-100 ml-8'
                : 'bg-gray-100 mr-8'
            }`}
          >
            <p className="text-sm font-semibold mb-1">
              {msg.role === 'user' ? '사용자' : 'AI'}
            </p>
            <p>{msg.content}</p>
            {/* 환각 탐지 결과 배지 (AI 답변에만 표시) */}
            {msg.role === 'assistant' && (
              <HallucinationBadge score={(msg as any).hhem_score} />
            )}
          </div>
        ))}
        {isLoading && (
          <div className="bg-gray-100 mr-8 p-3 rounded-lg animate-pulse">
            답변 생성 중...
          </div>
        )}
      </div>

      {/* 입력창 */}
      <form onSubmit={handleSubmit} className="flex gap-2">
        <input
          value={input}
          onChange={handleInputChange}
          placeholder="질문을 입력하세요..."
          className="flex-1 border rounded-lg px-4 py-2"
          disabled={isLoading}
        />
        <button
          type="submit"
          disabled={isLoading}
          className="bg-blue-500 text-white px-6 py-2 rounded-lg disabled:opacity-50"
        >
          전송
        </button>
      </form>
    </div>
  );
}

// 환각 탐지 점수 배지 컴포넌트
function HallucinationBadge({ score }: { score?: number }) {
  if (score === undefined) return null;

  const config =
    score >= 0.95
      ? { label: '신뢰 가능', color: 'bg-green-100 text-green-800' }
      : score >= 0.5
      ? { label: '확인 필요', color: 'bg-yellow-100 text-yellow-800' }
      : { label: '환각 의심', color: 'bg-red-100 text-red-800' };

  return (
    <span className={`text-xs px-2 py-1 rounded-full mt-2 inline-block ${config.color}`}>
      신뢰도 {(score * 100).toFixed(0)}% · {config.label}
    </span>
  );
}
```

---

## 4. 환경변수 설정

```bash
# .env.local

# AI 모델 프로바이더
ANTHROPIC_API_KEY=sk-ant-...
MODEL_PROVIDER=anthropic   # anthropic | openai | ollama

# 벡터 DB (PostgreSQL + pgvector)
DATABASE_URL=postgresql://user:pass@localhost:5432/ragdb

# 환각 탐지 서버 (Python FastAPI)
VERIFIER_API_URL=http://localhost:8000

# Langfuse (자체 호스팅 또는 클라우드)
LANGFUSE_PUBLIC_KEY=pk-lf-...
LANGFUSE_SECRET_KEY=sk-lf-...
LANGFUSE_BASE_URL=http://localhost:3001  # 자체 호스팅 시
```

---

## 5. 도구별 구현 난이도 및 우선순위

|도구|역할|Next.js 연동 난이도|우선순위|
|---|---|---|---|
|Vercel AI SDK + LangChain|RAG 파이프라인 + 스트리밍|⭐ 낮음 (공식 문서 풍부)|1순위|
|pgvector (PostgreSQL)|벡터 저장소|⭐⭐ 보통|1순위|
|Langfuse|로그 + 모니터링 + XAI|⭐ 낮음 (공식 SDK 제공)|2순위|
|HHEM-2.1-Open|환각 탐지 점수|⭐⭐⭐ 높음 (Python 서버 별도 구축)|2순위|
|DeepEval|자동 테스트|⭐⭐ 보통 (CI/CD 연동)|3순위|
|RAGAS|RAG 품질 평가|⭐⭐ 보통 (Python 연동)|3순위|
|LM Eval Harness|환각률 측정|⭐⭐⭐ 높음 (오프라인 벤치마크)|4순위|

---

## 6. 단계별 구현 로드맵

### Phase 1 — 기본 RAG 챗봇

```
✅ Next.js 프로젝트 생성
✅ Vercel AI SDK 설치 및 기본 채팅 구현
✅ LangChain + pgvector 연동
✅ 문서 업로드 및 임베딩 파이프라인
✅ 스트리밍 UI (useChat)
```

### Phase 2 — 환각 탐지 연동

```
✅ Python FastAPI 서버 구축
✅ HHEM-2.1-Open 모델 로드 및 API 구현
✅ Next.js → Python 검증 API 비동기 호출
✅ 프론트엔드 신뢰도 배지 표시
```

### Phase 3 — 모니터링 및 XAI

```
✅ Langfuse 자체 호스팅 (Docker Compose)
✅ 파이프라인 전 구간 트레이싱 연동
✅ 환각 발생 패턴 대시보드 구성
```

### Phase 4 — 테스트 자동화

```
✅ DeepEval CI/CD 연동 (GitHub Actions)
✅ RAGAS RAG 품질 평가 스크립트
✅ 환각률 수치 기반 배포 게이팅
```

---

## 7. 구현 불가 또는 대안이 필요한 항목

|항목|이유|대안|
|---|---|---|
|HHEM GPU 추론 on Vercel|Vercel GPU 미지원 + 5분 타임아웃|Python FastAPI 별도 서버 (AWS/GCP/Fly.io)|
|Ollama 로컬 모델 on Vercel|GPU 미지원|개발 환경 전용, 프로덕션은 Claude/GPT API|
|LM Eval Harness 실시간 실행|리소스 과다|배포 전 오프라인 배치 작업으로 실행|
|Arize Phoenix 자체 호스팅|별도 서버 필요|Langfuse로 대체 (기능 범위 동일)|

---

## 8. 패키지 설치 요약

```bash
# Next.js 프로젝트
npm install ai @ai-sdk/anthropic @ai-sdk/openai
npm install @langchain/core @langchain/openai @langchain/community
npm install langfuse
npm install drizzle-orm pg @types/pg

# Python 검증 서버
pip install fastapi uvicorn transformers torch
pip install deepeval ragas langchain
```

---

## 참고 링크

|도구|공식 문서|
|---|---|
|Vercel AI SDK|https://sdk.vercel.ai/docs|
|Vercel AI SDK RAG 가이드|https://sdk.vercel.ai/docs/guides/rag-chatbot|
|LangChain|https://docs.langchain.com|
|HHEM-2.1-Open|https://huggingface.co/vectara/hallucination_evaluation_model|
|DeepEval|https://docs.confident-ai.com|
|RAGAS|https://docs.ragas.io|
|Langfuse|https://langfuse.com/docs|

---

_작성 기준일: 2026년 6월 | Vercel AI SDK v6 기준_