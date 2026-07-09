  
이 문서는 프로젝트를 처음 pull 받은 상태에서 Step 2 검증/답변 테스트베드 UI/API를 실행하는 절차를 정리한다.  
  
Step 2는 Step 1 검색 결과를 입력으로 받아 Gemma LLM role adapter들을 실행하고, 생성 답변을 claim/evidence 단위로 검증한 뒤 GateDecision 결과에 따라 최종 답변을 렌더링한다. 따라서 Step 2 실행에는 기본적으로 다음 두 축이 필요하다.  
  
- Step 1 검색 인프라: Elasticsearch는 기본 실행에 필요하다.  
- Gemma gateway: Step 2 답변 생성/claim 추출/evidence linking/semantic verification/revision에 필요하다.  
  
기본 테스트베드는 `use_vector=false`로 실행할 수 있으므로 Chroma, embedding, reranker 모델은 처음에는 없어도 된다.  
  
## 1. 구성 요소  
  
| 구성 요소 | 역할 | 필수 여부 |  
|---|---|---|  
| Elasticsearch | Step 1 keyword/BM25 검색 백엔드 | 기본 실행에 필요 |  
| Chroma | Step 1 vector 검색 백엔드 | `use_vector=true`일 때 필요 |  
| Embedding model | query/document vector 생성용 로컬 BGE 모델 | `use_vector=true`일 때 필요 |  
| Reranker model | 검색 후보 재정렬용 로컬 BGE cross-encoder | `use_vector=true`일 때 필요 |  
| Gemma gateway | Step 2 LLM role adapter 실행 | Step 2에 필요 |  
| Step2 testbed | Step 1 검색 -> Step 2 검증 trace UI/API | 실행 대상 |  
  
## 2. 전제 조건  
  
Docker Desktop이 실행 중이어야 한다.  
  
PowerShell에서 프로젝트 루트로 이동한다.  
  
```powershell  
cd C:\Users\ijak\Desktop\TF_AI_harness_v0.6  
```  
  
Docker 연결을 확인한다.  
  
```powershell  
docker ps  
docker compose version  
```  
  
Gemma gateway가 접근 가능한지 확인한다. 이 환경에서는 `http://192.168.1.22:9080`을 사용한다.  
  
```powershell  
curl.exe http://192.168.1.22:9080/v1/models  
```  
  
`docker-compose.step1-runtime.yml`의 `step2-testbed` 서비스에서 `TF_GEMMA_BASE_URL` 값이 다르면 `http://192.168.1.22:9080`으로 맞추거나 compose override를 사용한다.  
  
## 3. Elasticsearch 실행  
  
Step 2 테스트베드는 내부에서 Step 1 검색을 먼저 실행한다. 기본 `use_vector=false` 경로에서는 Elasticsearch가 핵심 검색 백엔드다.  
  
```powershell  
docker compose -f docker-compose.step1.yml up -d --build  
```  
  
확인:  
  
```powershell  
curl.exe http://localhost:9201  
```  
  
정상이라면 Elasticsearch JSON 응답이 온다.  
  
## 4. Step2 UI/API 실행  
  
Step2 테스트베드는 `docker-compose.step1-runtime.yml`의 `step2-testbed` 서비스로 실행한다.  
  
```powershell  
docker compose -f docker-compose.step1-runtime.yml up -d --build step2-testbed  
```  
  
Step2 testbed UI/API는 이 프로젝트에서 로컬 Docker 컨테이너로 띄우며, 호스트 포트 `8089`로 노출된다. Gemma gateway 주소가 아니라 testbed의 로컬 API 주소다.  
  
브라우저에서 접속한다.  
  
```text  
http://localhost:8089  
```  
  
Step2 testbed API health 확인:  
  
```powershell  
curl.exe http://localhost:8089/api/health  
```  
  
응답에는 Step 1 index 상태와 실제로 testbed가 사용할 Gemma endpoint/model 정보가 포함된다. 여기의 `gemma_endpoint`가 `http://192.168.1.22:9080/v1/chat/completions`이면 Gemma 연결 설정은 새 주소를 보고 있는 것이다.  
  
```json  
{  
  "indexes": {    "elasticsearch": true,    "chroma": false,    "embedding_model": false,    "reranker_model": false  },  "gemma_endpoint": "http://192.168.1.22:9080/v1/chat/completions",  "gemma_model": "gemma-4-12b-it-qat-q4_0-gguf"}  
```  
  
처음 실행 가능한 최소 상태는 `elasticsearch=true`이고, `chroma/embedding_model/reranker_model=false`여도 `use_vector=false` 요청은 실행할 수 있다.  
  
## 5. API로 답변 trace 실행  
  
Step 2 파이프라인만 먼저 확인하려면 Intake 범위 선택을 우회한다. 현재 API의 기본값은 `use_intake=true`이므로, 최소 trace 확인 요청에서는 `use_intake=false`를 명시한다. 이 모드는 요청의 instruction 기본값으로 Step 1 검색과 Step 2 검증 trace를 바로 실행한다.  
  
주의: `/api/answer`는 `law_ids`를 직접 받을 수 있다. 민법만 대상으로 실행하려면 `law_ids = @("001706")`을 함께 보낸다.  
  
```powershell  
$body = @{  
  query = "민법 제2조 신의성실 원칙을 설명해줘."  
  law_ids = @("001706")  use_vector = $false  use_intake = $false  keyword_top_k = 5  rerank_top_k = 3} | ConvertTo-Json  
  
curl.exe -X POST http://localhost:8089/api/answer `  
  -H "Content-Type: application/json" `  -d $body```  
  
Intake까지 켜서 실행하려면 현재 법령 corpus가 복수 법령을 포함하므로 법령 범위가 필요하다. API에서 직접 `law_ids`를 보내거나, 자연어에서 "민법"을 추론하게 하려면 slot filler를 켠다.  
  
주의: slot filler를 켜면 법령 ID뿐 아니라 문서 ID 추론도 지원하기 위해 Mongo 문서 corpus catalog를 읽는다. Mongo(`host.docker.internal:27018`)가 준비되지 않은 최소 실행 환경에서는 503 `local_corpus_unavailable`이 날 수 있다. 이 경우 먼저 `use_intake=false`로 Step 2 trace를 확인한다.  
  
```powershell  
$body = @{  
  query = "민법 제2조 신의성실 원칙을 설명해줘."  
  law_ids = @("001706")  use_vector = $false  use_intake = $true  use_slot_filler = $false  keyword_top_k = 5  rerank_top_k = 3} | ConvertTo-Json  
  
curl.exe -X POST http://localhost:8089/api/answer `  
  -H "Content-Type: application/json" `  -d $body```  
  
정상 응답은 다음 필드를 포함한다.  
  
```text  
state  
rendered_answer  
retrieval  
ai_generated  
ai_documentation  
llm_call_metadata  
answer_candidate  
claims  
evidence_links  
verification_result  
findings  
gate_decision  
revision_iterations  
```  
  
`state`가 `rendering`이면 검증된 답변이 `rendered_answer`에 들어간다. `human_review`, `intake_blocked`, `error`면 최종 사용자 답변은 렌더링되지 않는다.  
  
## 6. AI 생성물 표시 및 추론과정 문서화 trace  
  
현재 Step2 응답은 AI 기본법 대응 문서화를 위해 다음 메타데이터를 포함한다.  
  
| 필드 | 의미 |  
|---|---|  
| `ai_generated` | 응답에 AI 생성 답변 후보 또는 렌더링된 AI 답변이 포함되었는지 |  
| `rendered_answer_ai_generated` | Gate를 통과해 최종 사용자 답변이 렌더링되었는지 |  
| `ai_generated_label` | UI/API에서 표시할 AI 생성물 안내 문구 |  
| `ai_documentation` | 설명/보관 계약 metadata |  
| `llm_call_metadata` | role별 LLM 호출 metadata |  
  
중요: 이 trace는 모델 내부 chain-of-thought를 저장하지 않는다. 저장/표시 대상은 시스템이 사용한 입력, 근거, 계약 객체, 검증 결과, Gate 판단이다.  
  
`ai_documentation`의 핵심 고정값:  
  
```json  
{  
  "schema_version": "ai_documentation_v1",  "documentation_purpose": "high_impact_ai_explanation_and_retention",  "chain_of_thought_stored": false,  "reasoning_content_stored": false,  "retention_required_years": 5}  
```  
  
`llm_call_metadata`는 prompt 본문, user message 본문, provider raw response, `reasoning_content`를 저장하지 않고 다음 같은 metadata만 남긴다.  
  
```text  
role_id  
model_profile_id  
prompt_profile_id  
context_bundle_id  
thinking  
temperature  
max_tokens  
provider  
model  
retry_attempts  
```  
  
관련 문서:  
  
```text  
docs/ai_basic_act/  
```  
  
UI에서 확인/다운로드:  
  
- `AI Documentation` 카드: `ai_documentation`, artifact coverage, LLM role 호출 metadata 요약을 확인한다.  
- `Raw trace JSON`: API 응답 전체를 그대로 확인한다.  
- `Download trace JSON`: 전체 Step2 trace를 JSON 파일로 받는다.  
- `Download AI documentation JSON`: 문서화에 필요한 핵심 필드만 JSON으로 받는다.  
- `Download AI documentation Markdown`: 설명/보관용 문서 초안을 Markdown으로 받는다.  
  
## 7. 선택형 JSONL TraceStore 사용  
  
5년 보관 정책 테스트를 위해 Step2 trace를 JSONL 파일로 저장할 수 있다. 저장은 기본 비활성화이며, `TF_TRACE_STORE_PATH`가 설정된 경우에만 켜진다.  
  
PowerShell에서 저장 경로를 지정한다.  
  
```powershell  
$env:TF_TRACE_STORE_PATH = "C:\Users\ijak\Desktop\TF_AI_harness_v0.6\var\answer_traces.jsonl"  
```  
  
Docker compose로 실행할 때는 `docker-compose.step1-runtime.yml`의 `step2-testbed.environment`에 다음 값을 추가하거나 compose override를 사용한다.  
  
```yaml  
TF_TRACE_STORE_PATH: /workspace/var/answer_traces.jsonl  
```  
  
컨테이너 안에서 `/workspace/var`에 쓰려면 volume이 필요하다. 예:  
  
```yaml  
volumes:  
  - ./models:/workspace/models:ro  - ./var:/workspace/var  
```  
  
Step2 요청을 실행한 뒤 저장 파일을 확인한다.  
  
```powershell  
Get-Content .\var\answer_traces.jsonl -Tail 1  
```  
  
저장 record에는 다음 필드가 자동으로 추가된다.  
  
```text  
trace_id  
schema_version = ai_trace_v1  
created_at  
retention_until  
```  
  
TraceStore는 저장 전에 다음 항목을 제거하거나 마스킹한다.  
  
| 처리 | 대상 |  
|---|---|  
| 제거 | `reasoning_content`, `raw`, `raw_response`, `provider_raw_response`, `chain_of_thought` |  
| 마스킹 | `secret`, `token`, `password`, `authorization`, `api_key`가 포함된 key |  
  
## 8. UI에서 실행할 때 현재 동작  
  
현재 Step2 UI는 다음 입력을 전송한다.  
  
```text  
query  
law_ids  
use_vector  
require_citations  
include_direct_quotes  
answer_style  
keyword_top_k  
```  
  
`law scope` select는 `/api/health`의 `legal_corpus_catalog`에서 채워진다. 민법 질문은 `민법 (001706)`을 선택한 상태로 실행한다.  
  
UI는 `use_intake`, `use_slot_filler`, `use_task_classifier`, `rerank_top_k`를 직접 보내지 않는다. 따라서 UI 실행은 API 기본값인 `use_intake=true`, `use_slot_filler=false` 경로로 들어간다. `law scope`를 비워 두면 복수 법령 범위 선택이 필요해 `state=intake_blocked`로 끝날 수 있다.  
  
처음에 전체 Step 2 trace를 확인하려면 위의 API 예시처럼 `use_intake=false`를 명시해서 호출한다. Intake와 slot filler까지 확인하려면 API에서 다음 값을 명시한다.  
  
```text  
use_vector = false  
use_intake = true  
use_slot_filler = true  
use_task_classifier = false  
keyword_top_k = 5  
rerank_top_k = 3  
```  
  
실제 흐름:  
  
```text  
사용자 질문  
 -> Intake TaskBrief 컴파일(use_intake=true인 경우)  
 -> Step 1 Elasticsearch keyword 검색  
 -> Step 1 결과를 ContextPackage로 bridge -> Step 2 AnswerGenerator -> ClaimExtractor -> EvidenceLinker -> SemanticVerifier -> GateDecision -> 필요하면 retrieve_more/revision loop -> 검증 통과 시 rendered_answer 출력  
```  
  
## 9. Chroma/vector 경로 사용  
  
`use_vector=true`를 켜려면 Chroma와 로컬 모델이 준비되어 있어야 한다.  
  
Chroma가 이미 있다면:  
  
```powershell  
docker start agent-memory-chroma  
```  
  
없다면 새로 만든다.  
  
```powershell  
docker run -d `  
  --name agent-memory-chroma `  -p 8001:8000 `  -v agent_memory_chroma:/chroma/chroma `  chromadb/chroma:latest```  
  
확인:  
  
```powershell  
curl.exe http://localhost:8001/api/v2/heartbeat  
```  
  
모델은 기본적으로 아래 경로를 기대한다.  
  
```text  
models/  
  embedding/  reranker/  
```  
  
Chroma와 모델을 준비한 뒤 Step2 테스트베드를 재시작한다.  
  
```powershell  
docker compose -f docker-compose.step1-runtime.yml restart step2-testbed  
```  
  
그 다음 요청에서 `use_vector=true`를 사용한다.  
  
```powershell  
$body = @{  
  query = "민법 제2조 신의성실 원칙을 설명해줘."  
  use_vector = $true  use_intake = $false  keyword_top_k = 5  rerank_top_k = 3} | ConvertTo-Json  
  
curl.exe -X POST http://localhost:8089/api/answer `  
  -H "Content-Type: application/json" `  -d $body```  
  
## 10. 수동 smoke 스크립트  
  
호스트 Python 환경에서 Step2 Gemma 단독 smoke를 실행할 수 있다.  
  
```powershell  
python scripts/step2_gemma_smoke.py  
```  
  
이 PowerShell에서 `python` 명령이 잡히지 않으면 프로젝트 가상환경 파이썬을 직접 쓴다.  
  
```powershell  
.\.venv\Scripts\python.exe scripts/step2_gemma_smoke.py  
```  
  
Step 1 검색까지 포함한 전체 흐름 smoke:  
  
```powershell  
$env:ELASTICSEARCH_URL = "http://localhost:9201"  
$env:CHROMA_URL = "http://localhost:8001"  
$env:TF_GEMMA_BASE_URL = "http://192.168.1.22:9080"  
$env:TF_GEMMA_TIMEOUT = "600"  
  
python scripts/step2_testbed_smoke.py "민법 제2조 신의성실 원칙을 설명해줘."  
```  
  
`python` 명령이 없으면 다음처럼 실행한다.  
  
```powershell  
.\.venv\Scripts\python.exe scripts/step2_testbed_smoke.py "민법 제2조 신의성실 원칙을 설명해줘."  
```  
  
기본은 `TF_TESTBED_USE_VECTOR=0`이다. vector 경로를 시험하려면 Chroma와 모델을 준비한 뒤 다음 값을 추가한다.  
  
```powershell  
$env:TF_TESTBED_USE_VECTOR = "1"  
```  
  
## 11. 중지 명령  
  
Step2 UI/API 중지:  
  
```powershell  
docker compose -f docker-compose.step1-runtime.yml stop step2-testbed  
```  
  
Elasticsearch 중지:  
  
```powershell  
docker compose -f docker-compose.step1.yml stop  
```  
  
Chroma 중지:  
  
```powershell  
docker stop agent-memory-chroma  
```  
  
## 12. 문제 확인 명령  
  
컨테이너 상태:  
  
```powershell  
docker ps -a  
```  
  
Step2 health:  
  
```powershell  
curl.exe http://localhost:8089/api/health  
```  
  
Step2 로그:  
  
```powershell  
docker compose -f docker-compose.step1-runtime.yml logs -f step2-testbed  
```  
  
Elasticsearch:  
  
```powershell  
curl.exe http://localhost:9201  
```  
  
Gemma gateway:  
  
```powershell  
curl.exe http://192.168.1.22:9080/v1/models  
```  
  
Chroma:  
  
```powershell  
curl.exe http://localhost:8001/api/v2/heartbeat  
```  
  
## 13. 자주 막히는 지점  
  
`/api/health`에서 `elasticsearch=false`면 Step 1 검색이 실패한다. `docker compose -f docker-compose.step1.yml up -d --build`를 먼저 실행한다.  
  
`/api/answer`가 `state=intake_blocked`, `validation.status=NEED_SCOPE_SELECTION`으로 끝나면 검색할 법령 범위가 선택되지 않은 상태다. UI에서는 `law scope`에서 `민법 (001706)`처럼 대상 법령을 선택한다. API에서는 `law_ids: ["001706"]`을 보낸다.  
  
Gemma gateway가 unreachable이면 Step 2 role adapter가 503 또는 `state=error`로 끝난다. `TF_GEMMA_BASE_URL`, 네트워크, gateway process를 확인한다.  
  
첫 응답이 오래 걸리는 것은 정상일 수 있다. 현재 `TF_GEMMA_TIMEOUT` 기본값은 600초이고, Step 2는 여러 LLM role을 순차적으로 호출한다.  
  
`use_vector=true`에서 실패하면 먼저 `use_vector=false`로 되돌려 Elasticsearch keyword 경로가 정상인지 확인한다. vector 경로는 Chroma, embedding model, reranker model 준비가 모두 필요하다.  
  
`use_intake=true`, `use_slot_filler=true`에서 `local_corpus_unavailable`과 `document corpus inventory unavailable: host.docker.internal:27018`이 나오면 Mongo 문서 corpus가 없는 상태다. 최소 Step 2 실행은 `use_intake=false`, `use_slot_filler=false`로 진행한다. slot filler까지 검증하려면 MongoDB(`agent-memory-mongodb`, host port `27018`)와 문서 corpus 색인이 필요하다.