  
이 문서는 이 프로젝트를 처음 pull 받은 상태에서 Step 1 검색 UI/API를 실행하는 절차를 정리한다.  
  
Step 1은 Gemma 없이도 실행할 수 있다. Gemma는 Step 2 답변 생성/검증에 필요하고, Step 1 검색 화면의 기본 keyword 검색에는 필요하지 않다.  
  
Step 1은 검색 후보와 근거 구성 상태를 확인하는 테스트베드다. AI 생성물 표시, 답변 생성/검증 trace, 5년 보관용 JSONL TraceStore는 Step 2 테스트베드에서 확인한다.  
  
## 1. 구성 요소  
  
Step 1 화면의 health badge는 다음 구성 요소 상태를 보여준다.  
  
| Badge | 역할 | 필수 여부 |  
|---|---|---|  
| Elasticsearch | keyword/BM25 검색 DB | 기본 실행에 필요 |  
| Chroma | vector 검색 DB | vector 검색을 켤 때 필요 |  
| Embedding | 텍스트를 vector로 바꾸는 로컬 BGE 모델 | vector 검색을 켤 때 필요 |  
| Reranker | 검색 후보를 BGE cross-encoder로 재정렬하는 로컬 모델 | BGE rerank를 켤 때 필요 |  
  
기본으로는 `use_vector=false`, keyword 검색 중심으로 실행하면 된다.  
  
## 2. 전제 조건  
  
- Docker Desktop이 실행 중이어야 한다.  
- PowerShell에서 아래 명령을 프로젝트 루트에서 실행한다.  
  
```powershell  
cd C:\Users\ijak\Desktop\TF_AI_harness_v0.6  
```  
  
Docker가 연결되는지 확인한다.  
  
```powershell  
docker ps  
docker compose version  
```  
  
## 3. Elasticsearch 실행  
  
Elasticsearch는 이 repo의 `docker-compose.step1.yml`에 정의되어 있다.  
  
```powershell  
docker compose -f docker-compose.step1.yml up -d --build  
```  
  
확인:  
  
```powershell  
curl.exe http://localhost:9201  
```  
  
정상이라면 Step1 UI의 `Elasticsearch` badge가 초록색이 된다.  
  
## 4. Step1 UI/API 실행  
  
Step1 UI는 `docker-compose.step1-runtime.yml`의 `step1-viz` 서비스로 실행한다.  
  
```powershell  
docker compose -f docker-compose.step1-runtime.yml up -d --build step1-viz  
```  
  
브라우저에서 접속한다.  
  
```text  
http://localhost:8088  
```  
  
API health 확인:  
  
```powershell  
curl.exe http://localhost:8088/api/health  
```  
  
예상 가능한 최소 정상 상태:  
  
```json  
{  
  "elasticsearch": true,  "chroma": false,  "embedding_model": false,  "reranker_model": false}  
```  
  
이 상태에서도 `use_vector=false`, `use_keyword=true` 요청의 keyword 검색은 실행할 수 있다.  
  
## 5. Chroma 실행  
  
Chroma는 이 repo의 compose 파일에 포함되어 있지 않다. 문서상 기존 `agent-memory-chroma` 컨테이너를 재사용하는 구조였으므로, 새 환경에서는 별도로 만들 수 있다.  
  
이미 컨테이너가 있다면:  
  
```powershell  
docker start agent-memory-chroma  
```  
  
없다면 새로 생성한다.  
  
```powershell  
docker run -d `  
  --name agent-memory-chroma `  -p 8001:8000 `  -v agent_memory_chroma:/chroma/chroma `  chromadb/chroma:latest```  
  
확인:  
  
```powershell  
curl.exe http://localhost:8001/api/v2/heartbeat  
```  
  
그 다음 Step1 UI 컨테이너를 재시작한다.  
  
```powershell  
docker compose -f docker-compose.step1-runtime.yml restart step1-viz  
```  
  
정상이라면 `Chroma` badge가 초록색이 된다.  
  
## 6. Embedding/Reranker 모델  
  
`Embedding`과 `Reranker`는 Gemma가 아니다. Step1 검색용 로컬 BGE 모델이다.  
  
프로젝트는 기본적으로 아래 경로를 기대한다.  
  
```text  
models/  
  embedding/  reranker/  
```  
  
현재 이 폴더가 없거나 비어 있으면 health에서 다음 두 항목은 false가 된다.  
  
```json  
{  
  "embedding_model": false,  "reranker_model": false}  
```  
  
이 상태에서는 다음 기능이 비활성화된다.  
  
- Chroma vector 검색  
- BGE reranker 재정렬  
  
하지만 Elasticsearch keyword 검색과 deterministic rerank fallback은 동작한다.  
  
## 7. 현재 가능한 실행 모드  
  
모델이 없는 상태에서는 UI에서 다음처럼 사용한다.  
  
```text  
use_vector = false  
rerank_mode = deterministic  
```  
  
참고: Step1 UI의 rerank 선택 기본값은 코드상 `bge`다. 모델이 없으면 내부적으로 deterministic fallback을 쓰지만, 처음 실행 확인 단계에서는 `deterministic`을 직접 선택하는 편이 상태를 해석하기 쉽다.  
  
실제 흐름:  
  
```text  
사용자 질문  
 -> Elasticsearch keyword/BM25 검색  
 -> deterministic rerank fallback -> 결과 표시  
```  
  
모델과 Chroma가 모두 준비되면 다음 흐름도 가능해진다.  
  
```text  
사용자 질문  
 -> Embedding 모델로 query vector 생성  
 -> Chroma vector 검색  
 -> Elasticsearch keyword 검색  
 -> merge/dedup -> BGE reranker 재정렬  
 -> 결과 표시  
```  
  
## 8. 중지 명령  
  
Step1 UI 중지:  
  
```powershell  
docker compose -f docker-compose.step1-runtime.yml stop step1-viz  
```  
  
Elasticsearch 중지:  
  
```powershell  
docker compose -f docker-compose.step1.yml stop  
```  
  
Chroma 중지:  
  
```powershell  
docker stop agent-memory-chroma  
```  
  
## 9. Step1과 AI 생성물 표시의 경계  
  
Step1 UI(`http://localhost:8088`)는 AI 답변을 생성하지 않는다. 따라서 다음 항목은 Step1 화면에서 확인하지 않는다.  
  
```text  
ai_generated  
ai_generated_label  
ai_documentation  
llm_call_metadata  
rendered_answer  
TraceStore 저장  
```  
  
위 항목은 Step2 UI/API(`http://localhost:8089`)의 `/api/answer` 응답에서 확인한다.  
  
## 10. 문제 확인 명령  
  
컨테이너 상태:  
  
```powershell  
docker ps -a  
```  
  
Step1 health:  
  
```powershell  
curl.exe http://localhost:8088/api/health  
```  
  
Elasticsearch:  
  
```powershell  
curl.exe http://localhost:9201  
```  
  
Chroma:  
  
```powershell  
curl.exe http://localhost:8001/api/v2/heartbeat  
```  
  
Step1 UI 로그:  
  
```powershell  
docker compose -f docker-compose.step1-runtime.yml logs -f step1-viz  
```  
  
Elasticsearch 로그:  
  
```powershell  
docker logs -f tf-ai-harness-elasticsearch-step1  
```