
## 전체 구성  
  
| 구성 요소 | 주소 | 역할 |  
|---|---|---|  
| Step2 testbed | http://localhost:8089 | 로컬 UI/API |  
| Elasticsearch | http://localhost:9201 | keyword/BM25 검색 |  
| Chroma | http://localhost:8001 | vector 검색 |  
| Gemma LLM | http://192.168.1.21:9080 | 원격 LLM |  
| Embedding/Reranker | http://192.168.1.21:9081 | 원격 Step1 모델 서버 |  
  
`deploy/testbed/compose.yaml`의 `step2-testbed`는 아래 원격 모델 주소를 바라본다.  
  
```text  
TF_GEMMA_BASE_URL=http://192.168.1.21:9080  
TF_EMBEDDING_ENDPOINT=http://192.168.1.21:9081/v1/embeddings  
TF_RERANKER_ENDPOINT=http://192.168.1.21:9081/v1/rerank  
TF_MODEL_SERVER_HEALTH_URL=http://192.168.1.21:9081/health  
```  
  
## 빠른 실행  
  
1. 원격 모델 서버 확인  
  
```powershell  
curl.exe http://192.168.1.21:9080/v1/models  
curl.exe http://192.168.1.21:9081/health  
```  
  
2. Elasticsearch 실행  
  
```powershell  
docker compose -f docker-compose.core.yml --profile ingestion up -d --build elasticsearch  
curl.exe http://localhost:9201/_cluster/health  
```  
  
3. Chroma 실행  
  
```powershell  
docker start agent-memory-chroma  
curl.exe http://localhost:8001/api/v2/heartbeat  
```  
  
`agent-memory-chroma` 컨테이너가 없으면 새로 만든다.  
  
```powershell  
docker run -d `  
  --name agent-memory-chroma `  --restart unless-stopped `  -p 8001:8000 `  -v agent_memory_chroma:/chroma/chroma `  chromadb/chroma:0.5.23```  
  
4. Step2 testbed 실행  
  
```powershell  
docker compose -f deploy/testbed/compose.yaml up -d --build step2-testbed  
```  
  
5. 브라우저 접속  
  
```text  
http://localhost:8089  
```  
  
6. 최종 health 확인  
  
```powershell  
curl.exe http://localhost:8089/api/health  
```  
  
정상 예시:  
  
```json  
{  
  "indexes": {    "elasticsearch": true,    "chroma": true,    "embedding_model": true,    "reranker_model": true  },  "gemma_endpoint": "http://192.168.1.21:9080/v1/chat/completions",  "gemma_model": "gemma-4-12b-it-qat-q4_0-gguf"}  
```  
  
## `embed down` 복구  
  
Step2 화면에 아래처럼 뜨면 `9081` 모델 서버가 죽었거나 접근 불가인 상태다.  
  
```text  
ES up · Chroma up · embed down  
```  
  
먼저 로컬 PC에서 원격 health를 확인한다.  
  
```powershell  
curl.exe http://192.168.1.21:9081/health  
```  
  
연결 실패면 `192.168.1.21` 서버에서 model-server를 올린다.  
  
```bash  
cd ~/TF_AI_harness_model_serverdocker compose -f docker-compose.step1-runtime.yml up -d --builddocker compose -f docker-compose.step1-runtime.yml pscurl http://127.0.0.1:9081/healthcurl http://192.168.1.21:9081/health```  
  
주의: `~/TF_AI_harness_model_server`에는 기본 compose 파일명인 `compose.yaml` 또는 `docker-compose.yml`이 없다. 파일명이 `docker-compose.step1-runtime.yml`이므로 반드시 `-f docker-compose.step1-runtime.yml`을 붙인다.  
  
아래처럼 실행하면 실패한다.  
  
```bash  
docker compose up -d --build```  
  
실패 메시지:  
  
```text  
no configuration file provided: not found  
```  
  
정상 health 예시:  
  
```json  
{  
  "embedding_model": {    "name": "BAAI/bge-m3",    "path_exists": true  },  "reranker_model": {    "name": "BAAI/bge-reranker-v2-m3",    "path_exists": true  }}  
```  
  
embedding 실제 호출 확인:  
  
```bash  
curl -X POST http://192.168.1.21:9081/v1/embeddings \  -H "Content-Type: application/json" \  -d '{"model":"BAAI/bge-m3","input":"test"}'```  
  
원격 모델 서버 로그:  
  
```bash  
docker compose -f docker-compose.step1-runtime.yml logs -f --tail=100```  
  
원격 모델 서버 중지/재시작:  
  
```bash  
docker compose -f docker-compose.step1-runtime.yml stopdocker compose -f docker-compose.step1-runtime.yml start```  
  
## 운영 명령  
  
### Elasticsearch  
  
```powershell  
docker compose -f docker-compose.core.yml --profile ingestion up -d --build elasticsearch  
docker compose -f docker-compose.core.yml --profile ingestion stop elasticsearch  
docker compose -f docker-compose.core.yml --profile ingestion start elasticsearch  
docker compose -f docker-compose.core.yml --profile ingestion up -d --build --force-recreate elasticsearch  
docker logs -f tf-ai-harness-elasticsearch-step1  
```  
  
### Chroma  
  
```powershell  
docker start agent-memory-chroma  
docker stop agent-memory-chroma  
curl.exe http://localhost:8001/api/v2/heartbeat  
```  
  
### Step2 testbed  
  
```powershell  
docker compose -f deploy/testbed/compose.yaml up -d --build step2-testbed  
docker compose -f deploy/testbed/compose.yaml stop step2-testbed  
docker compose -f deploy/testbed/compose.yaml start step2-testbed  
docker compose -f deploy/testbed/compose.yaml up -d --build --force-recreate step2-testbed  
docker compose -f deploy/testbed/compose.yaml logs -f step2-testbed  
```  
  
## 로컬 testbed model-server  
  
이 repo의 `deploy/testbed/compose.yaml`에도 `model-server` 서비스가 있다.  
  
```powershell  
docker compose -f deploy/testbed/compose.yaml up -d --build model-server  
curl.exe http://localhost:9081/health  
```  
  
하지만 현재 `step2-testbed` 서비스는 원격 `192.168.1.21:9081`을 바라본다. 로컬 `model-server`를 실제로 사용하려면 `TF_EMBEDDING_ENDPOINT`, `TF_RERANKER_ENDPOINT`, `TF_MODEL_SERVER_HEALTH_URL` 값을 로컬 또는 compose service 주소로 바꾼 뒤 `step2-testbed`를 재생성해야 한다.  
  
로컬 model-server 운영 명령:  
  
```powershell  
docker compose -f deploy/testbed/compose.yaml stop model-server  
docker compose -f deploy/testbed/compose.yaml start model-server  
docker compose -f deploy/testbed/compose.yaml up -d --build --force-recreate model-server  
```  
  
## 상태 점검 기준  
  
필요한 로컬 컨테이너:  
  
```text  
agent-memory-chroma                         0.0.0.0:8001->8000  
tf-ai-harness-elasticsearch-step1           0.0.0.0:9201->9200  
tf_ai_harness_step1_runtime-step2-testbed-1 0.0.0.0:8089->8000  
```  
  
원격 `192.168.1.21`에서 응답해야 하는 포트:  
  
```text  
192.168.1.21:9080  Gemma LLM  
192.168.1.21:9081  Embedding/Reranker model-server  
```  
  
Step2 health에서 아래 네 값이 모두 `true`면 검색/검증 테스트를 실행할 수 있다.  
  
```text  
elasticsearch=true  
chroma=true  
embedding_model=true  
reranker_model=true  
```  
  
문제별 확인 지점:  
  
| 증상 | 먼저 확인할 것 |  
|---|---|  
| `embed down` | `curl.exe http://192.168.1.21:9081/health` |  
| `ES down` | `curl.exe http://localhost:9201/_cluster/health` |  
| `Chroma down` | `curl.exe http://localhost:8001/api/v2/heartbeat` |  
| Step2 UI 접속 실패 | `docker compose -f deploy/testbed/compose.yaml logs -f step2-testbed` |