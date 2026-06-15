
## 1. four_layer_deck 기준 구현 위치

four_layer_deck의 4개 레이어 중 **Layer 2와 Layer 3**을 구현했다.

```
Layer 1 · Intake          ❌ 미구현
Layer 2 · Knowledge       ✅ 구현
Layer 3 · RAG Harness     ✅ 구현 (LLM 연결 완료)
Layer 4 · Verification    ❌ 미구현
```

문서의 권장 개발 순서(Phase) 기준으로는 **Phase 1(Knowledge) + Phase 2(RAG Harness)** 에 해당한다.

---

## 2. 사용한 모델 및 서버

|항목|선택|비고|
|---|---|---|
|LLM 서버|Ollama (로컬)|localhost:11434, API 키 불필요, 외부 전송 없음|
|LLM 모델|llama3.2|Ollama로 로컬 실행|
|임베딩 모델|BAAI/bge-m3|한국어 포함 다국어 지원, HuggingFace|
|리랭커 모델|BAAI/bge-reranker-v2-m3|Cross-Encoder 기반, HuggingFace|
|키워드 검색|BM25 (rank_bm25)|벡터 검색 보완용|

>**four_layer_deck 문서 기준 권장 모델**
> - Generator: claude-opus-4-8, Semantic Verifier: claude-sonnet-4-6
> - 현재는 연습 목적으로 Ollama(llama3.2)로 대체하여 구현함.

---

## 3. 구현한 전체 과정

### Layer 2 · Knowledge (loader.py, embedder.py)

**Step 1 · 문서 입력 (loader.py)**
- PDF 파일을 pdfplumber로 페이지별 텍스트 추출
- txt, md 등 일반 텍스트 파일도 지원

**Step 2 · 청킹 (loader.py)**
- 마침표/느낌표/물음표 기준으로 문장 단위 분리
- max_length(300자) 초과 시 청크 저장 후 새로 시작
- 20자 미만 조각은 제거

**Step 3 · 임베딩 (embedder.py)**
- BAAI/bge-m3 모델로 각 청크를 벡터(숫자 배열)로 변환
- 텍스트 → 1024차원 벡터로 변환

**Step 4 · 벡터 저장 (embedder.py)**
- 변환된 벡터와 원본 청크 텍스트를 vectors.pt 파일로 저장 (PyTorch)
- 이미 vectors.pt가 있으면 인덱싱 과정 스킵

---

### Layer 3 · RAG Harness (retriever.py, reranker.py, main.py)

**Step 5 · 질문 임베딩 (retriever.py)**
- 사용자 질문을 문서와 동일한 bge-m3 모델로 벡터 변환
- 같은 모델을 써야 벡터 공간이 같아서 비교 가능

**Step 6 · 하이브리드 검색 (retriever.py)**
- 벡터 검색(Cosine Similarity) 60% + BM25 키워드 검색 40% 가중 합산
- 벡터 검색: 의미적으로 비슷한 청크 탐색
- BM25 검색: 질문 단어가 직접 포함된 청크 탐색
- 상위 5개 후보 추출

**Step 7 · 리랭킹 (reranker.py)**
- BAAI/bge-reranker-v2-m3 (Cross-Encoder) 모델로 재정렬
- 검색 후보 5개를 질문-문서 쌍으로 묶어 정밀 스코어링
- 최종 상위 3개만 선별

**Step 8 · 프롬프트 조립 (main.py)**
- 리랭킹 결과 상위 3개 청크 + 사용자 질문을 합쳐 LLM용 프롬프트 생성
- 시스템 프롬프트: "문서에 없는 내용은 절대 답하지 않습니다"

**Step 9 · LLM 호출 및 답변 생성 (main.py)**
- Ollama 로컬 서버(localhost:11434)에 프롬프트 전송
- llama3.2 모델이 참고 문서 기반으로 최종 답변 생성

---

## 4. 파일 구조 요약

```
core-trustlayer/
├── loader.py      # Step 1-2: 문서 로드 + 청킹
├── embedder.py    # Step 3-4: 임베딩 + 벡터 저장/로드
├── retriever.py   # Step 5-6: 질문 임베딩 + 하이브리드 검색
├── reranker.py    # Step 7: 리랭킹
├── main.py        # Step 8-9: 프롬프트 조립 + LLM 호출 (진입점)
└── vectors.pt     # 생성된 벡터 파일 (자동 생성)
```

---

## 5. 미구현 항목 (다음 단계)

```
Layer 1 · Intake       : 사용자 질문 정제, Task Brief 생성
Layer 4 · Verification : 답변의 근거 검증, Gate Decision
```