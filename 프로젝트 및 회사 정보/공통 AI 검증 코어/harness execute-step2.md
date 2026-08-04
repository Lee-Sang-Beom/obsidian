현재 기준으로 오래된 `docker-compose.step1*.yml` 명령은 모두 빼고, 실제 남아 있는 진입점만 기준으로 정리하겠습니다. 모델 쪽은 “Step2가 실제로 쓰는 원격 9080/9081”과 “로컬 testbed model-server 9081”이 다르기 때문에 그 구분을 문서 안에 명확히 넣겠습니다.아래처럼 지금 구조 기준으로 바꾸면 됩니다. 오래된 `docker-compose.step1.yml`, `docker-compose.step1-runtime.yml` 기준 내용은 제거했습니다.

```markdown
# Step2 Testbed 실행 가이드

이 문서는 현재 프로젝트 구조에서 Step2 테스트베드 UI/API를 실행하는 최소 절차를 정리한다.

현재 주요 포트는 다음과 같다.

| 대상 | 주소 | 비고 |
|---|---|---|
| Step2 testbed | http://localhost:8089 | 로컬 UI/API |
| Elasticsearch | http://localhost:9201 | keyword/BM25 검색 |
| Chroma | http://localhost:8001 | vector 검색 |
| Gemma LLM | http://192.168.1.22:9080 | 원격 LLM |
| Embedding/Reranker | http://192.168.1.22:9081 | 원격 모델 서버 |

## 1. Model 확인/실행

현재 `step2-testbed`는 기본적으로 원격 모델 서버를 사용한다.

```text
TF_GEMMA_BASE_URL=http://192.168.1.22:9080
TF_EMBEDDING_ENDPOINT=http://192.168.1.22:9081/v1/embeddings
TF_RERANKER_ENDPOINT=http://192.168.1.22:9081/v1/rerank
```

따라서 이 프로젝트에서 Step2 테스트베드만 띄울 때는 보통 모델 컨테이너를 따로 올리지 않는다.

모델 서버 접근 확인:

```powershell
curl.exe http://192.168.1.22:9080/v1/models
curl.exe http://192.168.1.22:9081/health
```

로컬 testbed model-server를 별도로 올리고 싶다면:

```powershell
docker compose -f deploy/testbed/compose.yaml up -d --build model-server
```

중지:

```powershell
docker compose -f deploy/testbed/compose.yaml stop model-server
```

다시 시작:

```powershell
docker compose -f deploy/testbed/compose.yaml start model-server
```

이미지까지 다시 빌드해서 재생성:

```powershell
docker compose -f deploy/testbed/compose.yaml up -d --build --force-recreate model-server
```

로컬 model-server 확인:

```powershell
curl.exe http://localhost:9081/health
```

주의: 현재 `step2-testbed` compose는 원격 `192.168.1.22:9081`을 바라본다. 로컬 `model-server`를 실제로 사용하려면 `TF_EMBEDDING_ENDPOINT`, `TF_RERANKER_ENDPOINT`, `TF_MODEL_SERVER_HEALTH_URL` 값을 로컬 또는 compose service 주소로 바꿔야 한다.

## 2. Elasticsearch 실행

Elasticsearch는 현재 `docker-compose.core.yml`의 `ingestion` profile로 이동했다.

실행:

```powershell
docker compose -f docker-compose.core.yml --profile ingestion up -d --build elasticsearch
```

상태 확인:

```powershell
curl.exe http://localhost:9201/_cluster/health
```

정상 예시:

```json
{
  "cluster_name": "docker-cluster",
  "status": "green"
}
```

중지:

```powershell
docker compose -f docker-compose.core.yml --profile ingestion stop elasticsearch
```

다시 시작:

```powershell
docker compose -f docker-compose.core.yml --profile ingestion start elasticsearch
```

이미지까지 다시 빌드해서 재생성:

```powershell
docker compose -f docker-compose.core.yml --profile ingestion up -d --build --force-recreate elasticsearch
```

로그 확인:

```powershell
docker logs -f tf-ai-harness-elasticsearch-step1
```

## 3. Chroma 실행

Chroma는 Step2의 vector 검색에 필요하다. 현재 테스트베드는 `http://host.docker.internal:8001`을 바라보므로, host port `8001`로 열려 있어야 한다.

기존 컨테이너가 있으면:

```powershell
docker start agent-memory-chroma
```

없으면 기존 volume을 붙여 새로 만든다.

```powershell
docker run -d `
  --name agent-memory-chroma `
  --restart unless-stopped `
  -p 8001:8000 `
  -v agent_memory_chroma:/chroma/chroma `
  chromadb/chroma:0.5.23
```

확인:

```powershell
curl.exe http://localhost:8001/api/v2/heartbeat
```

중지:

```powershell
docker stop agent-memory-chroma
```

다시 시작:

```powershell
docker start agent-memory-chroma
```

## 4. Step2 테스트베드 실행

Step2 테스트베드는 현재 `deploy/testbed/compose.yaml`의 `step2-testbed` 서비스로 실행한다.

실행:

```powershell
docker compose -f deploy/testbed/compose.yaml up -d --build step2-testbed
```

브라우저 접속:

```text
http://localhost:8089
```

health 확인:

```powershell
curl.exe http://localhost:8089/api/health
```

정상 예시:

```json
{
  "indexes": {
    "elasticsearch": true,
    "chroma": true,
    "embedding_model": true,
    "reranker_model": true
  },
  "gemma_endpoint": "http://192.168.1.22:9080/v1/chat/completions"
}
```

중지:

```powershell
docker compose -f deploy/testbed/compose.yaml stop step2-testbed
```

다시 시작:

```powershell
docker compose -f deploy/testbed/compose.yaml start step2-testbed
```

이미지까지 다시 빌드해서 재생성:

```powershell
docker compose -f deploy/testbed/compose.yaml up -d --build --force-recreate step2-testbed
```

로그 확인:

```powershell
docker compose -f deploy/testbed/compose.yaml logs -f step2-testbed
```

## 5. 전체 상태 확인

현재 필요한 컨테이너 확인:

```powershell
docker ps
```

기대 상태:

```text
agent-memory-chroma                         0.0.0.0:8001->8000
tf-ai-harness-elasticsearch-step1           0.0.0.0:9201->9200
tf_ai_harness_step1_runtime-step2-testbed-1 0.0.0.0:8089->8000
```

Step2 health에서 아래 네 값이 모두 `true`면 검색/검증 테스트를 실행할 수 있다.

```text
elasticsearch=true
chroma=true
embedding_model=true
reranker_model=true
```
```