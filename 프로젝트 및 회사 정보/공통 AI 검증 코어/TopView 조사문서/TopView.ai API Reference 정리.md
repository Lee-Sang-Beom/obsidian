
> 사용자가 제공한 Topview API Reference 링크 묶음을 기준으로, 공식 문서의 OpenAPI 스키마와 원본 Markdown 표를 다시 확인해 요청/응답 필드 설명을 보강한 문서입니다.

## 문서 작성 기준

- 기준일: 2026-07-27
- Base URL: `https://api.topview.ai`
- 공식 문서: `https://docs.topview.ai`
- 개요/규칙 문서는 원본 Markdown 표가 깨지지 않도록 표 형식을 보존했습니다.

## 공통 응답 구조

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |

## 공통 인증 헤더

| Header | 의미 |
|---|---|
| `Topview-Uid` | Topview 계정의 사용자 ID입니다. 대부분의 API 호출에 필요합니다. |
| `Authorization` | `Bearer <API Key>` 형식의 API Key 인증 헤더입니다. |

## 공통 처리 패턴

- 생성형 API는 대체로 `Submit Task`로 `taskId`를 받은 뒤 `Query Task`로 상태와 결과를 조회합니다.
- 작업 상태는 보통 `init`, `running`, `success`, `fail` 중 하나입니다.
- 실패 시 `errorMsg` 또는 `errorCode`를 확인합니다.
- 결과 URL은 장기 보관용으로 보장되지 않을 수 있으므로, 필요한 산출물은 별도 저장소에 복사하는 것이 안전합니다.

## 기본 규칙

### [Authentication](https://docs.topview.ai/reference/authentication-1)
→ API 호출 시 `Topview-Uid`와 `Authorization: Bearer <API Key>` 헤더 필수

#### 개요

- Topview API는 모든 호출에서 `Topview-Uid`와 `Authorization` 헤더를 사용합니다.
- API Key는 Topview 웹 앱의 계정 공간에서 `API Settings` 메뉴를 통해 확인하거나 생성합니다.

#### Request Payload / Query

_실제 API 엔드포인트가 아니라 인증 방법을 설명하는 문서입니다._

#### Response Result

_응답 스키마가 없습니다._

### [Response Code](https://docs.topview.ai/reference/error-response)
→ 응답 코드 및 에러 메시지 규칙

#### 개요

- Topview API의 공통 비즈니스 응답 코드입니다. HTTP 상태 코드와 별개로 JSON 본문의 `code`를 함께 확인해야 합니다.

| Code | Message |
|---|---|
| `200` | Success |
| `4000` | Request parameter error |
| `4001` | Request data format does not match! |
| `4002` | Request digital signature does not match! |
| `4003` | Required parameter cannot be null |
| `4004` | Resource not found! |
| `4005` | Name duplicated |
| `4006` | Request refuse |
| `4007` | Exists unfinished task, please wait until the previous task is completed. |
| `4100` | Credit not enough |
| `5000` | Internal server error! |
| `5001` | Feign invoke error |
| `5003` | Server is busy, please try again later! |
| `5004` | I/O error occurred |
| `5005` | Unknown error occurred |
| `6001` | Security problem detect |

#### Request Payload / Query

_실제 API 엔드포인트가 아니라 공통 오류 코드 설명 문서입니다._

#### Response Result

_응답 스키마가 없습니다._

### [Points Deduction Rules](https://docs.topview.ai/reference/rules-for-consumption-rules)
→ 크레딧/포인트 차감 규칙

#### 개요

- 기능별 크레딧 차감 규칙을 정리한 문서입니다. 실제 차감 방식은 API별 문서와 요금 정책 변경에 따라 달라질 수 있으므로, 최신 과금 문서를 함께 확인해야 합니다.

| Task | 차감 규칙 요약 |
|---|---|
| Avatar Marketing Video | Export 완료 후 5 points 차감 |
| Product Avatar | Product Replacement는 이미지 1개 생성당 1 point. Avatar 생성은 모드와 대본 길이 또는 오디오 길이에 따라 차감 |
| Video Avatar | Avatar 모드, 스크립트 글자 수, 오디오 길이에 따라 차감 |
| Product Anyshoot | Product Replacement는 이미지 1개당 1 point. Image-to-Video는 품질, 길이, 생성 개수에 따라 차감 |
| Photo Avatar | TTS 또는 업로드 오디오 길이에 따라 초 단위로 차감. Avatar2, Avatar4, Avatar4 Fast의 단가가 다름 |
| CommonTask/Image2Video | Product Anyshoot의 Image-to-Video 규칙과 동일 |
| Image Translate | 이미지 수, 대상 언어 수, 계수 2를 곱해 차감 |
| Character Swap | 비디오 길이 기준 0.3/s, 최대 60초 |
| Pa_Download_Without_Watermark | 1 point |
| Voice Clone | 1 point |
| Text2Voice | 0.1 point |

#### Request Payload / Query

_실제 API 엔드포인트가 아니라 과금 규칙 설명 문서입니다._

#### Response Result

_응답 스키마가 없습니다._

### [Query User Credit](https://docs.topview.ai/reference/query_user_credit)
→ 사용자 크레딧 잔액 및 만료 시간 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/user/credit/detail` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

_요청 본문/쿼리 파라미터 없음_

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `String` | 응답 코드입니다. |
| `message` | `String` | 응답 메시지입니다. |
| `result` | `Object` | 결과 객체입니다. |
| `result.uid` | `String` | 사용자 ID입니다. |
| `result.remainCredit` | `Number` | 남은 크레딧 잔액입니다. |
| `result.expiredTime` | `String` | ISO 8601 형식의 만료 시간입니다. |
| `result.apiKeyMap` | `Object` | API Key와 관련 작업의 매핑 정보입니다. |
| `result.apiKeyMap.apiKey` | `String` | 작업에 사용된 API Key입니다. |
| `result.apiKeyMap[].uid` | `String` | 사용자 ID입니다. |
| `result.apiKeyMap[].date` | `String` | 작업이 생성된 날짜/시간입니다. ISO 8601 형식입니다. |
| `result.apiKeyMap[].costCredit` | `Number` | 차감된 크레딧입니다. |
| `result.apiKeyMap[].taskId` | `String` | task ID입니다. |
| `result.apiKeyMap[].taskType` | `String` | 작업 유형입니다. 예: `voice_clone`, `m2v`. |

### [Query Credit Logs](https://docs.topview.ai/reference/query_credit_logs)
→ 크레딧 사용 내역(로그) 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/user/credit/logs` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskType` | `string` | 작업 유형입니다. 조회 또는 필터링 조건으로 사용합니다. (enum: m2v, common_task_image2video, video_avatar, product_avatar_image2video, voice_clone, product_anyfit, pa_download_without_watermark, pa2_replace_product_image, pa2_image2video, product_anyfit_v2_product_model) |
| `startTime` | `string` | UTC 시간입니다. 형식: yyyy MM dd hh: mm: ss, 예: 2022-07-01 12:01:01 |
| `endTime` | `string` | UTC 시간입니다. 형식: yyyy MM dd hh: mm: ss, 예: 2022-07-02 13:01:01 |
| `pageNo` | `number` | 페이지 번호입니다. |
| `pageSize` | `number` | 페이지당 항목 수입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `String` | 응답 코드입니다. |
| `message` | `String` | 응답 메시지입니다. |
| `result` | `Object` | 결과 객체입니다. |
| `result.total` | `Number` | 조회 가능한 전체 항목 수입니다. |
| `result.pageNo` | `Number` | 페이지 번호입니다. |
| `result.pageSize` | `Number` | 페이지당 항목 수입니다. |
| `result.data` | `Array` | task 객체 목록입니다. |
| `result.data[].uid` | `String` | 사용자 ID입니다. |
| `result.data[].apiKey` | `String` | 작업에 사용된 API Key입니다. |
| `result.data[].date` | `String` | 작업이 생성된 날짜/시간입니다. ISO 8601 형식입니다. |
| `result.data[].costCredit` | `Number` | 차감된 크레딧입니다. |
| `result.data[].taskId` | `String` | task ID입니다. |
| `result.data[].taskType` | `String` | 작업 유형입니다. 예: `voice_clone`, `m2v`. |

### [Query User Space](https://docs.topview.ai/reference/get_new-endpoint-42)
→ 사용자 디스크 공간 사용량 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/user/space` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

_요청 본문/쿼리 파라미터 없음_

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `uid` | `string` | 사용자 ID입니다. |
| `usedDiskSpace` | `object` | 사용 중인 디스크 공간 정보입니다. |
| `usedDiskSpace.size` | `number` | 사용 중인 디스크 공간 크기입니다. 단위는 bytes입니다. |
| `usedDiskSpace.unit` | `string` | 사용 중인 디스크 공간의 단위입니다. |
| `maxDiskSpace` | `object` | 최대 디스크 공간 정보입니다. |
| `maxDiskSpace.size` | `number` | 최대 디스크 공간 크기입니다. 단위는 bytes입니다. |
| `maxDiskSpace.unit` | `string` | 최대 디스크 공간의 단위입니다. |

## Boards 관련

### [List Boards](https://docs.topview.ai/reference/listboards)
→ 계정 내 보드 목록 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/boards/list` |

#### 개요

현재 사용자 계정에 속한 Board 목록을 페이지 단위로 조회합니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer` token 형식입니다. (필수) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `pageNo` | `integer` | 페이지 번호입니다. |
| `pageSize` | `integer` | 페이지당 항목 수입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.list` | `array` | Board 객체 목록입니다. |
| `result.list[].boardId` | `string` | Board ID입니다. |
| `result.list[].name` | `string` | Board 이름입니다. |
| `result.list[].uid` | `string` | 사용자 ID입니다. |
| `result.list[].ownerName` | `string` | 소유자 이름입니다. |
| `result.list[].taskCount` | `integer` | 이 Board에 포함된 task 수입니다. |
| `result.list[].recentThumbnails` | `array` | 최근 task 썸네일 목록입니다. |
| `result.list[].members` | `array` | Board 멤버 목록입니다. |
| `result.list[].shareTokens` | `array` | 공유 token 목록입니다. Board 소유자에게만 표시됩니다. |
| `result.list[].isSystemDefault` | `boolean` | 시스템 기본 Board인지 여부입니다. |
| `result.list[].syncFlag` | `boolean` | 동기화 상태입니다. `true`는 동기화됨, `false`는 미동기화입니다. |
| `result.list[].gmtCreate` | `string` | 생성 시각입니다. |
| `result.list[].gmtModify` | `string` | 마지막 수정 시각입니다. |
| `result.list[].isOwner` | `boolean` | 현재 사용자가 Board 소유자인지 여부입니다. |
| `result.total` | `integer` | 전체 Board 수입니다. |
| `result.pageNo` | `integer` | 페이지 번호입니다. |
| `result.pageSize` | `integer` | 페이지당 항목 수입니다. |

### [List Board Tasks](https://docs.topview.ai/reference/listboardtasks)
→ 특정 보드에 속한 작업(Task) 목록 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/boards/tasks/list` |

#### 개요

- 특정 Board 안의 task 목록을 페이지 단위로 조회합니다.
- media type, rating, tool category, tool type 기준 필터링과 정렬을 지원합니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer` token 형식입니다. (필수) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `boardId` | `string` | Board ID입니다. |
| `pageNo` | `integer` | 페이지 번호입니다. |
| `pageSize` | `integer` | 페이지당 항목 수입니다. |
| `mediaType` | `string` | 미디어 유형으로 필터링합니다 (선택) (enum: all, image, video, audio) |
| `rating` | `string` | 평점으로 필터링합니다 (선택) (enum: all, 0, 1, 2, 3, unrated) |
| `toolCategory` | `string` | 도구 카테고리로 필터링합니다 (선택) (enum: image, video, avatar, voice, music) |
| `toolType` | `string` | 도구 유형으로 필터링합니다 (선택) |
| `sortField` | `string` | 정렬 기준 필드입니다 (선택) (enum: gmtCreate, gmtModify, completedAt) |
| `sortOrder` | `string` | 정렬 방향입니다. `asc` 또는 `desc`를 사용합니다 (선택) (enum: asc, desc) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.list` | `array` | 조회 결과 목록입니다. |
| `result.list[].taskId` | `string` | task ID입니다. |
| `result.list[].boardTaskId` | `string` | Board task ID입니다. |
| `result.list[].boardId` | `string` | Board ID입니다. |
| `result.list[].sortWeight` | `number` | 정렬 가중치입니다. |
| `result.list[].uid` | `string` | 사용자 ID입니다. |
| `result.list[].userName` | `string` | 생성자 이름입니다. |
| `result.list[].toolType` | `string` | 도구 유형입니다. |
| `result.list[].toolCategory` | `string` | 도구 카테고리입니다. |
| `result.list[].status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.list[].mediaType` | `string` | 미디어 유형입니다. `image`/`video`/`audio` 중 하나입니다. |
| `result.list[].rating` | `integer` | 평점입니다. 0~3 범위입니다. |
| `result.list[].parameters` | `object` | task 입력 파라미터입니다. |
| `result.list[].result` | `object` | 결과 객체입니다. |
| `result.list[].errorMessage` | `string` | 오류 메시지입니다. |
| `result.list[].errorCode` | `string` | 오류 코드입니다. |
| `result.list[].creditsCost` | `integer` | 소비된 크레딧입니다. |
| `result.list[].creditsPayerUid` | `string` | 크레딧 결제/차감 대상 사용자 ID입니다. |
| `result.list[].creditsPayerName` | `string` | 크레딧 결제/차감 대상 사용자 이름입니다. |
| `result.list[].gmtCreate` | `string` | 생성 시각입니다. |
| `result.list[].gmtModify` | `string` | 마지막 수정 시각입니다. |
| `result.list[].completedAt` | `string` | 완료 시각입니다. |
| `result.list[].isPinned` | `boolean` | task가 고정되었는지 여부입니다. |
| `result.list[].pinnedOriginalSortWeight` | `number` | 고정하기 전 원래 정렬 가중치입니다. |
| `result.list[].groupIds` | `array` | Group ID 목록입니다. |
| `result.list[].unresolvedCount` | `integer` | 미해결 댓글 수입니다. |
| `result.list[].useUnlimitMode` | `boolean` | Unlimited mode 사용 여부입니다. |
| `result.list[].estimateInfo` | `object` | 작업 예상 비용/처리 정보입니다. |
| `result.list[].estimateInfo.queueCount` | `integer` | 대기열에 있는 task 수입니다. 현재 task는 제외합니다. |
| `result.list[].estimateInfo.estimatedWaitSeconds` | `integer` | 예상 대기 시간입니다. 단위는 초입니다. |
| `result.list[].estimateInfo.estimatedProcessSeconds` | `integer` | 예상 처리 시간입니다. 단위는 초입니다. |
| `result.total` | `integer` | 조건에 맞는 전체 항목 수입니다. |
| `result.pageNo` | `integer` | 페이지 번호입니다. |
| `result.pageSize` | `integer` | 페이지당 항목 수입니다. |

### [Get Board Task Detail](https://docs.topview.ai/reference/getboardtaskdetail)
→ 특정 작업(Task)의 상세 정보 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/boards/tasks/detail` |

#### 개요

특정 Board task의 상세 정보를 조회합니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer` token 형식입니다. (필수) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.boardTaskId` | `string` | Board task ID입니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.sortWeight` | `number` | 정렬 가중치입니다. |
| `result.uid` | `string` | 사용자 ID입니다. |
| `result.userName` | `string` | 생성자 이름입니다. |
| `result.toolType` | `string` | 도구 유형입니다. |
| `result.toolCategory` | `string` | 도구 카테고리입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.mediaType` | `string` | 미디어 유형입니다. |
| `result.rating` | `integer` | 평점입니다. 0~3 범위입니다. |
| `result.parameters` | `object` | task 입력 파라미터입니다. |
| `result.result` | `object` | 결과 객체입니다. |
| `result.errorMessage` | `string` | 오류 메시지입니다. |
| `result.errorCode` | `string` | 오류 코드입니다. |
| `result.creditsCost` | `integer` | 소비된 크레딧입니다. |
| `result.creditsPayerUid` | `string` | 크레딧 결제/차감 대상 사용자 ID입니다. |
| `result.creditsPayerName` | `string` | 크레딧 결제/차감 대상 사용자 이름입니다. |
| `result.gmtCreate` | `string` | 생성 시각입니다. |
| `result.gmtModify` | `string` | 마지막 수정 시각입니다. |
| `result.completedAt` | `string` | 완료 시각입니다. |
| `result.isPinned` | `boolean` | task가 고정되었는지 여부입니다. |
| `result.pinnedOriginalSortWeight` | `number` | 고정하기 전 원래 정렬 가중치입니다. |
| `result.groupIds` | `array` | Group ID 목록입니다. |
| `result.unresolvedCount` | `integer` | 미해결 댓글 수입니다. |
| `result.useUnlimitMode` | `boolean` | Unlimited mode 사용 여부입니다. |
| `result.estimateInfo` | `object` | 작업 예상 비용/처리 정보입니다. |
| `result.estimateInfo.queueCount` | `integer` | 대기열에 있는 task 수입니다. |
| `result.estimateInfo.estimatedWaitSeconds` | `integer` | 예상 대기 시간입니다. 단위는 초입니다. |
| `result.estimateInfo.estimatedProcessSeconds` | `integer` | 예상 처리 시간입니다. 단위는 초입니다. |

### [Batch Delete Tasks](https://docs.topview.ai/reference/batchdeletetasks)
→ 여러 작업을 한 번에 삭제

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/boards/tasks/batch-delete` |

#### 개요

- Board 안의 여러 task를 한 번에 삭제합니다.
- 동기 처리 API이며, `taskIds`로 특정 task만 삭제하거나 `filter` 조건에 맞는 task 전체를 삭제할 수 있습니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer` token 형식입니다. (필수) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `boardId` | `string` | Board ID입니다. |
| `selectAll` | `boolean` | `true`이면 조건에 맞는 모든 task를 삭제하고, `false`이면 지정한 task만 삭제합니다. `true`일 때는 `filter`가 필요하며 `taskIds`는 무시됩니다. (기본값: False) |
| `taskIds` | `array` | 삭제할 task ID 목록입니다. `selectAll=false`일 때 필수이며, `selectAll=true`일 때는 무시됩니다. |
| `excludeTaskIds` | `array` | 삭제 대상에서 제외할 task ID 목록입니다. `selectAll=true`일 때만 적용됩니다. 사용자가 수동으로 선택 해제한 task를 제외할 때 사용합니다. |
| `filter` | `object` | 배치 작업에 사용할 필터 조건입니다. `selectAll=true`일 때만 적용됩니다. |
| `filter.mediaType` | `string` | 미디어 유형 필터입니다 (enum: all, image, video, audio) |
| `filter.rating` | `string` | 평점 필터입니다 (enum: all, 0, 1, 2, 3, unrated) |
| `filter.ratingOperator` | `string` | 범위 조회에 사용할 평점 비교 연산자입니다 (enum: >=, >, <=, <, =) |
| `filter.ratingValue` | `integer` | 범위 조회에 사용할 평점 값입니다 |
| `filter.toolCategory` | `string` | 도구 카테고리 필터입니다 (enum: image, video, avatar, voice, music) |
| `filter.toolType` | `string` | 도구 유형 필터입니다. 단일 값이며 하위 호환성을 위해 제공됩니다 |
| `filter.toolTypes` | `array` | 도구 유형 필터 목록입니다. OR 조건으로 조회합니다 |
| `filter.creatorUids` | `array` | 생성자 UID 목록입니다. OR 조건으로 조회합니다 |
| `filter.groupIds` | `array` | Group ID 목록입니다. OR 조건으로 조회합니다 |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |

### [Submit Batch Download](https://docs.topview.ai/reference/submitbatchdownload)
→ 여러 작업을 ZIP 파일로 묶어 다운로드 요청

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/boards/tasks/batch-download/submit` |

#### 개요

- Board task 여러 개를 ZIP 파일로 묶는 batch download 작업을 제출합니다.
- 비동기 처리 API이며, 상태 조회에 사용할 `downloadTaskId`를 반환합니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer` token 형식입니다. (필수) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `boardId` | `string` | Board ID입니다. |
| `selectAll` | `boolean` | `true`이면 조건에 맞는 모든 task를 다운로드하고, `false`이면 지정한 task만 다운로드합니다. `true`일 때는 `filter`가 필요하며 `taskIds`는 무시됩니다. (기본값: False) |
| `taskIds` | `array` | 다운로드할 task ID 목록입니다. `selectAll=false`일 때 필수이며, `selectAll=true`일 때는 무시됩니다. |
| `excludeTaskIds` | `array` | 다운로드 대상에서 제외할 task ID 목록입니다. `selectAll=true`일 때만 적용됩니다. 사용자가 수동으로 선택 해제한 task를 제외할 때 사용합니다. |
| `filter` | `object` | 배치 작업에 사용할 필터 조건입니다. `selectAll=true`일 때만 적용됩니다. |
| `filter.mediaType` | `string` | 미디어 유형 필터입니다 (enum: all, image, video, audio) |
| `filter.rating` | `string` | 평점 필터입니다 (enum: all, 0, 1, 2, 3, unrated) |
| `filter.ratingOperator` | `string` | 범위 조회에 사용할 평점 비교 연산자입니다 (enum: >=, >, <=, <, =) |
| `filter.ratingValue` | `integer` | 범위 조회에 사용할 평점 값입니다 |
| `filter.toolCategory` | `string` | 도구 카테고리 필터입니다 (enum: image, video, avatar, voice, music) |
| `filter.toolType` | `string` | 도구 유형 필터입니다. 단일 값이며 하위 호환성을 위해 제공됩니다 |
| `filter.toolTypes` | `array` | 도구 유형 필터 목록입니다. OR 조건으로 조회합니다 |
| `filter.creatorUids` | `array` | 생성자 UID 목록입니다. OR 조건으로 조회합니다 |
| `filter.groupIds` | `array` | Group ID 목록입니다. OR 조건으로 조회합니다 |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.downloadTaskId` | `string` | 상태 조회에 사용할 download task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.progress` | `integer` | 진행률입니다. |
| `result.processedCount` | `integer` | 처리된 파일 수입니다. |
| `result.totalCount` | `integer` | 처리할 전체 파일 수입니다. |
| `result.downloadUrl` | `string` | `status=success`일 때 제공되는 다운로드 URL입니다. |
| `result.fileCount` | `integer` | `status=success`일 때 ZIP에 포함된 파일 수입니다. |
| `result.totalSize` | `integer` | `status=success`일 때 ZIP 파일 크기입니다. 단위는 bytes입니다. |
| `result.expiresAt` | `string` | `status=success`일 때 다운로드 링크 만료 시간입니다. |
| `result.gmtCreate` | `string` | task 생성 시각입니다. |
| `result.completedAt` | `string` | `status=success` 또는 `fail`일 때 task 완료 시각입니다. |
| `result.errorCode` | `string` | 오류 코드입니다. |
| `result.errorMessage` | `string` | 오류 메시지입니다. |

### [Get Batch Download Status](https://docs.topview.ai/reference/getbatchdownloadstatus)
→ 배치 다운로드 작업 상태 조회 및 다운로드 URL 획득

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/boards/tasks/batch-download` |

#### 개요

- batch download 작업의 진행 상태를 조회합니다.
- 완료되면 다운로드 가능한 URL을 함께 반환합니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer` token 형식입니다. (필수) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `boardId` | `string` | Board ID입니다. |
| `downloadTaskId` | `string` | submit endpoint에서 반환된 download task ID입니다. (필수) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.downloadTaskId` | `string` | Download task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.progress` | `integer` | 진행률입니다. |
| `result.processedCount` | `integer` | 이미 처리된 파일 수입니다. |
| `result.totalCount` | `integer` | 처리할 전체 파일 수입니다. |
| `result.downloadUrl` | `string` | CloudFront signed 다운로드 URL (`status=success`일 때만). 만료 시간이 있는 임시 URL입니다. |
| `result.fileCount` | `integer` | `status=success`일 때 ZIP에 정상 포함된 파일 수입니다. |
| `result.totalSize` | `integer` | `status=success`일 때 전체 ZIP 파일 크기입니다. 단위는 bytes입니다. |
| `result.expiresAt` | `string` | `status=success`일 때 다운로드 URL 만료 시간입니다. 이 시간이 지나면 링크를 사용할 수 없습니다. |
| `result.gmtCreate` | `string` | task 생성 시각입니다. |
| `result.completedAt` | `string` | `status=success` 또는 `fail`일 때 task 완료 시각입니다. |
| `result.errorCode` | `string` | Error code (`status=fail`일 때만). Examples: FILE_NOT_FOUND, PERMISSION_DENIED, STORAGE_ERROR, etc. |
| `result.errorMessage` | `string` | 오류 메시지입니다. |

## Storyboard 관련

### [Storyboard Submit Task](https://docs.topview.ai/reference/storyboard-submit-task)
→ 스토리보드 생성 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/common_task/storyboard/task/submit` |

#### 개요

- Storyboard 이미지 생성 task를 비동기로 제출합니다.
- 제출 성공 시 결과 조회에 사용할 `taskId`를 반환합니다.
- 사용자의 `story` 텍스트와 선택적 참조 이미지를 스토리보드 grid preview 이미지로 변환합니다.
- 지원 모델: `GPT Image 2`, `Nano Banana 2`, `Nano Banana Pro`.
- `story`는 필수이며 최대 5000자입니다.
- 공개 API의 `aspectRatio`는 `16:9`, `9:16`, `1:1`을 지원합니다.
- `resolution`, `gridMode`, `gridSize`, `referenceFileIds` 제약은 아래 Body Params를 기준으로 확인합니다.
- 크레딧은 task 완료 후 차감되며, 실제 차감량은 query 응답의 `costCredit`에서 확인합니다.
- 주요 오류: `4000` parameter 오류, `4100` 크레딧 부족, `5000` 내부 서버 오류.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수; 기본값: uid) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer {apiKey}` 형식입니다. (필수; 기본값: Bearer apiKey) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `story` | `string` | Story text The system breaks it into storyboard keyframes and generates a grid preview image. Max 5000 characters (필수) |
| `model` | `string` | Model name (필수). 허용 값: `GPT Image 2`, `Nano Banana 2`, `Nano Banana Pro` (대소문자를 구분하지 않음; model code also accepted) (필수; enum: GPT Image 2, Nano Banana 2, Nano Banana Pro) |
| `aspectRatio` | `string` | Aspect ratio (선택). Public API only supports `16:9`, `9:16`, `1:1`; default `16:9` (enum: 16:9, 9:16, 1:1) |
| `resolution` | `string` | Resolution GPT Image 2 / Nano Banana Pro: `1K`/`2K`/`4K`; Nano Banana 2: `512p`/`1K`/`2K`/`4K` (필수) |
| `gridMode` | `string` | 스토리보드 패널 수 결정 방식입니다. `auto`는 자동 추론, `preset`은 사전 설정 수, `custom`은 사용자 지정 수를 사용합니다. 기본값은 `preset`입니다. (enum: auto, preset, custom) |
| `gridSize` | `string` | Storyboard/grid panel count (선택). `preset` mode: `4` / `9` / `25`; `custom` mode: 2~25. Default `9` |
| `targetDurationSeconds` | `integer` | 스토리보드 계획에 참고할 목표 영상 전체 길이입니다. 단위는 초이며 기본값은 15입니다. |
| `referenceFileIds` | `array` | Reference image fileId list (선택). Must be uploaded via the upload API first. 최대 개수는 모델별로 다릅니다 (GPT Image 2: 16, Nano Banana 2: 14, Nano Banana Pro: 6) |
| `boardId` | `string` | 연결할 Board ID입니다. (선택). 전달하면 `aigc-backend`에 대응되는 `boardTask`가 생성되며, 응답에 `boardId`가 포함될 수 있습니다. |
| `noticeUrl` | `string` | task 완료 시 호출할 callback URL입니다. (선택). 설정하면 응답에 `noticeUuid`가 포함되며, task 완료 시 이 URL로 알림을 전송합니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 제출 직후 접수 상태입니다. 접수되면 항상 `success`이며, 실제 실행 상태는 query API에서 `init`/`running`/`success`/`fail`로 확인합니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.subTaskId` | `string` | 일부 비즈니스 시나리오에서 반환되는 Sub-task ID입니다. 일반적으로는 이 API에서 반환되지 않습니다. |
| `result.boardTaskIds` | `array` | Board와 연결된 경우 반환되는 Board sub-task ID 목록입니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.noticeUuid` | `string` | 비동기 callback 알림 UUID입니다. request body에 `noticeUrl`이 설정된 경우 반환됩니다. |

### [Storyboard Query Task](https://docs.topview.ai/reference/storyboard-query-task)
→ 스토리보드 작업 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/common_task/storyboard/task/query` |

#### 개요

스토리보드 작업 상태와 결과를 조회합니다.

- 진행 중이면 `images`는 비어 있거나 처리 중 상태입니다.
- 성공 시 생성된 스토리보드 그리드 이미지의 `fileId`와 접근 URL을 반환합니다.
- `costCredit`은 성공 후 확정되는 실제 차감 크레딧입니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | User ID; must match the task owner (필수; 기본값: uid) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer {apiKey}` 형식입니다. (필수; 기본값: Bearer apiKey) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `boolean` | `true`이면 `images[].filePath` prefers CloudFront CDN URLs; default false returns S3 URLs (기본값: False) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.costCredit` | `number` | 차감된 크레딧입니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.images` | `array` | task 성공 시 반환되는 스토리보드 grid 이미지입니다. Storyboard task는 보통 grid preview 이미지 1개를 반환합니다. |
| `result.images[].status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.images[].fileId` | `string` | fileId입니다. |
| `result.images[].filePath` | `string` | 파일 경로 또는 접근 URL입니다. |
| `result.images[].width` | `integer` | 너비입니다. |
| `result.images[].height` | `integer` | 높이입니다. |
| `result.images[].errorMsg` | `string` | 오류 메시지입니다. |
| `result.images[].boardTaskId` | `string` | task가 Board에서 시작된 경우 반환되는 Board Task ID입니다. |

## AI Music 관련

### [AI Music Submit Task](https://docs.topview.ai/reference/ai-music-submit-task)
→ AI 음악 생성 작업 제출(가사/스타일 기반)

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/common_task/ai_music/task/submit` |

#### 개요

- AI Music 생성 task를 비동기로 제출합니다.
- 제출 성공 시 결과 조회에 사용할 `taskId`를 반환합니다.
- 지원 모델: `Topview Music`, `Minimax Music 2.6`. 모델명은 대소문자를 구분하지 않습니다.
- `instrumental=true`이면 `styles`가 필수이고 `lyrics`는 무시됩니다.
- `instrumental=false`이거나 생략하면 `lyrics` 또는 `styles` 중 하나 이상이 필요합니다.
- `enhancePrompt=true`이면 비어 있지 않은 `styles`가 필요합니다.
- `referenceAudio`를 전달하면 생성 전 오디오 형식과 길이를 검증합니다.
- 크레딧은 task 완료 후 차감되며, 실제 차감량은 query 응답의 `costCredit`에서 확인합니다.
- 주요 오류: `4012` invalid model, `4013`~`4022` 파라미터/참조 오디오 오류, `4100` 크레딧 부족.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수; 기본값: uid) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer {apiKey}` 형식입니다. (필수; 기본값: Bearer apiKey) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `model` | `string` | AI music 모델 이름입니다. (필수). 허용 값: `Topview Music`, `Minimax Music 2.6` (대소문자를 구분하지 않음) (필수; enum: Topview Music, Minimax Music 2.6) |
| `instrumental` | `boolean` | instrumental 음악(보컬 없음)을 생성할지 여부입니다. `true`이면 `styles`가 필수이고 `lyrics`는 무시됩니다. `false`이거나 생략하면 `lyrics` 또는 `styles` 중 하나 이상이 필요합니다. |
| `lyrics` | `string` | 가사 텍스트입니다. `instrumental=false`일 때 `lyrics` 또는 `styles` 중 하나 이상이 필요하며, `instrumental=true`이면 무시됩니다. 최대 길이는 모델별로 다릅니다. Topview Music 5000자, Minimax Music 2.6 3500자. |
| `styles` | `string` | 음악 스타일 설명입니다. `instrumental=true` 또는 `enhancePrompt=true`일 때 필수입니다. `instrumental=false`일 때는 `lyrics` 또는 `styles` 중 하나 이상이 필요합니다. 최대 길이는 모델별로 다릅니다. |
| `enhancePrompt` | `boolean` | `styles` 설명을 비동기로 보강할지 여부입니다. 활성화하면 LLM으로 style 텍스트를 최적화한 뒤 생성 작업에 전달합니다. 비어 있지 않은 `styles`가 필요합니다. |
| `referenceAudio` | `object` | 참조 오디오입니다. 제공하면 생성 작업 제출 전에 오디오 형식, 길이, 크기를 검증합니다. |
| `referenceAudio.fileId` | `string` | 참조 오디오 fileId입니다. |
| `referenceAudio.fileName` | `string` | 참조 오디오 파일 이름입니다. |
| `referenceAudio.clipStart` | `number` | 클립 시작 시간입니다. 단위는 초입니다. 생략하면 기본값은 0입니다. (선택) |
| `referenceAudio.clipEnd` | `number` | 클립 종료 시간입니다. 단위는 초입니다. 생략하면 원본 오디오 길이를 사용합니다. (선택) |
| `boardId` | `string` | 연결할 Board ID입니다. (선택). 전달하면 `aigc-backend`에 대응되는 `boardTask`가 생성되며, 응답에 `boardId`와 `boardTaskIds`가 포함될 수 있습니다. |
| `noticeUrl` | `string` | task 완료 시 호출할 callback URL입니다. (선택). 설정하면 응답에 `noticeUuid`가 포함되며, task 완료 시 이 URL로 알림을 전송합니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 제출 직후 접수 상태입니다. 접수되면 항상 `success`이며, 실제 실행 상태는 query API에서 `init`/`running`/`success`/`fail`로 확인합니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.subTaskId` | `string` | 일부 비즈니스 시나리오에서 반환되는 Sub-task ID입니다. 일반적으로는 이 API에서 반환되지 않습니다. |
| `result.boardTaskIds` | `array` | Board와 연결된 경우 반환되는 Board sub-task ID 목록입니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.noticeUuid` | `string` | 비동기 callback 알림 UUID입니다. request body에 `noticeUrl`이 설정된 경우 반환됩니다. |

### [AI Music Query Task](https://docs.topview.ai/reference/ai-music-query-task)
→ AI 음악 생성 작업 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/common_task/ai_music/task/query` |

#### 개요

AI Music 작업 상태와 결과를 조회합니다.

- 진행 중이면 `outputs`가 비어 있습니다.
- 성공 시 생성된 오디오의 `fileId`와 접근 URL을 반환합니다.
- `costCredit`은 성공 후 확정되는 실제 차감 크레딧입니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | User ID; must match the task owner (필수; 기본값: uid) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer {apiKey}` 형식입니다. (필수; 기본값: Bearer apiKey) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `boolean` | `true`이면 `outputs[].url` prefers CloudFront CDN URLs; default false returns S3 URLs (기본값: False) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.costCredit` | `number` | 차감된 크레딧입니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.styles` | `string` | task 제출 시 전달한 원본 style 설명입니다. |
| `result.enhancePrompt` | `boolean` | 제출 시 style enhancement가 활성화되었는지 여부입니다. |
| `result.enhancePromptStyle` | `string` | 보강된 style 텍스트입니다. `enhancePrompt=true`이고 보강에 성공한 경우 반환되며, 실제 생성 엔진에 전달되는 style 설명입니다. |
| `result.outputs` | `array` | task 성공 시 반환되는 생성 오디오 결과입니다. 현재는 생성당 1개 항목을 반환합니다. |
| `result.outputs[].fileId` | `string` | fileId입니다. |
| `result.outputs[].url` | `string` | 접근 URL입니다. |

## Voice 관련

### [Instant Voice Clone API](https://docs.topview.ai/reference/instant-voice-clone-api)
→ 참조 오디오 + 텍스트 기반 즉시 음성 복제 개요

#### 개요

- 즉시 음성 복제 기능의 사용 흐름을 설명하는 개요 문서입니다.
- `Submit Task`로 참조 오디오와 텍스트를 전달하고, `Query Task`로 결과 오디오 URL과 fileId를 조회합니다.
- 실제 호출 필드는 하위 `Instant Voice Clone Submit Task`와 `Instant Voice Clone Query Task` 섹션을 기준으로 확인합니다.

#### Request Payload / Query

_개요/사용법 문서이므로 별도 request payload가 없습니다. 실제 요청 필드는 하위 endpoint 문서를 확인합니다._

#### Response Result

_개요/사용법 문서이므로 별도 response schema가 없습니다. 실제 응답 필드는 하위 endpoint 문서를 확인합니다._

### [Instant Voice Clone Submit Task](https://docs.topview.ai/reference/submitinstantvoiceclonetasken)
→ 즉시 음성 복제 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/instant_voice_clone/task/submit` |

#### 개요

- Instant Voice Clone task를 비동기로 제출하고 `taskId`를 받습니다.
- 최종 결과는 query endpoint로 polling합니다.
- V1 모델은 `Index TTS`, `Fish Audio S2 Pro`를 지원합니다.
- `referenceAudioFileId`는 wav 오디오 fileId여야 하며, 출력 형식은 mp3입니다.
- `Fish Audio S2 Pro`는 `emotionMode`, `emotionVector`, `emotionText`를 지원하지 않습니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수; 기본값: uid) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer {apiKey}` 형식입니다. (필수; 기본값: Bearer apiKey) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `model` | `string` | Display model name (필수). 허용 값: `Index TTS`, `Fish Audio S2 Pro` (필수; enum: Index TTS, Fish Audio S2 Pro) |
| `text` | `string` | Voice script text (필수) |
| `referenceAudioFileId` | `string` | 참조 오디오 fileId입니다. |
| `emotionMode` | `string` | Emotion mode (선택): `slider` or `prompt`. Supported by `Index TTS` only (enum: slider, prompt) |
| `emotionVector` | `string` | Slider emotion vector (선택). `emotionMode=slider`일 때 필수입니다. |
| `emotionText` | `string` | Prompt emotion text (선택). `emotionMode=prompt`일 때 필수입니다. |
| `boardId` | `string` | Board ID입니다. |
| `noticeUrl` | `string` | task 완료 시 호출할 callback URL입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.boardTaskIds` | `array` | Board 연동이 활성화된 경우 반환됩니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.noticeUuid` | `string` | `noticeUrl`이 제공된 경우 반환됩니다. |

### [Instant Voice Clone Query Task](https://docs.topview.ai/reference/queryinstantvoiceclonetasken)
→ 즉시 음성 복제 결과 조회 및 음성 파일 URL 획득

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/instant_voice_clone/task/query` |

#### 개요

- `taskId`로 task 상태와 결과를 조회합니다.
- 완료되지 않은 task는 `running`으로 반환됩니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | User ID, must match task owner (필수; 기본값: uid) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer {apiKey}` 형식입니다. (필수; 기본값: Bearer apiKey) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `boolean` | CloudFront 가속 URL을 반환할지 여부입니다. 기본값은 `false`입니다. (기본값: False) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.costCredit` | `number` | 차감된 크레딧입니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.model` | `string` | 제출 시 사용한 표시용 모델 이름입니다. |
| `result.text` | `string` | 제출한 스크립트 텍스트입니다. |
| `result.outputAudio` | `object` | Output audio info (returned 성공 시) |
| `result.outputAudio.fileId` | `string` | fileId입니다. |
| `result.outputAudio.url` | `string` | 접근 URL입니다. |
| `result.outputAudio.duration` | `number` | 길이입니다. |
| `result.outputAudio.format` | `string` | 출력 오디오 형식입니다. |

## Image 관련

### [Image Character Swap API Usage](https://docs.topview.ai/reference/image-character-swap-api-usage)
→ 이미지 속 인물을 다른 인물로 교체하는 API 개요

#### 개요

- 이미지 속 인물 또는 캐릭터를 참조 이미지의 인물로 교체하는 기능의 사용 흐름입니다.
- 실제 호출은 `Image Character Swap Submit Task`로 작업을 제출하고 `Image Character Swap Query Task`로 결과를 조회합니다.

#### Request Payload / Query

_개요/사용법 문서이므로 별도 request payload가 없습니다. 실제 요청 필드는 하위 endpoint 문서를 확인합니다._

#### Response Result

_개요/사용법 문서이므로 별도 response schema가 없습니다. 실제 응답 필드는 하위 endpoint 문서를 확인합니다._

### [Image Character Swap Submit Task](https://docs.topview.ai/reference/v1-character-swap-task-submit)
→ 이미지 인물 교체 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/character_swap/task/submit` |

#### 개요

이미지 캐릭터/인물 교체 task를 제출합니다.

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `inputModelFileId` | `string` | 예시: 9022415045c042fa91f8a1839cc8034e (필수) |
| `inputTemplateFileId` | `string` | 예시: 629816a027bd4b5b83ac3356b06c7c86 (필수) |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: fail, success) |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Image Character Swap Query Task](https://docs.topview.ai/reference/v1-character-swap-task-query)
→ 이미지 인물 교체 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/character_swap/task/query` |

#### 개요

이미지 캐릭터/인물 교체 task 결과를 조회합니다.

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `boolean` | 결과 URL을 CloudFront URL 우선으로 받을지 여부입니다. (기본값: false) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, fail, success) |
| `result.outputFileId` | `string` | 생성된 결과 파일의 ID입니다. |
| `result.outputImageUrl` | `string` | 생성된 이미지 결과 URL입니다. |
| `result.costCredit` | `number` | 차감된 크레딧입니다. |
| `result.errorCode` | `string` | 오류 코드입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |

### [Image Mask Drawing Submit Task](https://docs.topview.ai/reference/image_mask_drawing_submit_task)
→ 이미지 마스크 드로잉 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/segment_anything/task/submit` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `videoProcessTaskId` | `string` | The video processing task ID obtained in the first step (필수) |
| `inputInfo` | `object` | 입력 파일 또는 입력 리소스 정보를 담는 객체입니다. (필수) |
| `inputInfo.index` | `string` | The index of the selected frame (필수) |
| `inputInfo.modifyPoints` | `array` | Modify regional point information Note: modifyPoints and protectPoints cannot be empty at the same time. 예시: "modifyPoints": [ [ 624.2696629213483, 627.2320675105485 ], [ 624.2696629213483, 699.915611814346 ] ] |
| `inputInfo.protectPoints` | `array` | Protection area location information. 예시: "protectPoints": [ [ 233.25842696629212, 411.873417721519 ] ] |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 작업 상태입니다. 보통 `init`, `running`, `success`, `fail` 중 하나입니다. (필수; enum: success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Image Mask Drawing Query Task](https://docs.topview.ai/reference/image_mask_drawing_query_task)
→ 이미지 마스크 드로잉 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/segment_anything/task/query` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `string` | 결과 URL을 CloudFront URL 우선으로 받을지 여부입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.mask` | `string` | Modify the mask image of the area (base64) (필수) |
| `result.protectMask` | `string` | Mask image of the protected area (base64) (필수) |
| `result.costCredit` | `number` | 차감된 크레딧입니다. |

### [Image Translate API Usage](https://docs.topview.ai/reference/image-translate-api-usage)
→ 이미지 내 텍스트 번역 API 개요

#### 개요

- 이미지 안의 텍스트 영역을 번역하고, 번역된 이미지를 다시 생성하는 기능의 사용 흐름입니다.
- 번역 작업 제출/조회와 생성 작업 제출/조회가 분리되어 있으므로, `Translate Task`와 `Generate Task`를 단계별로 사용합니다.

#### Request Payload / Query

_개요/사용법 문서이므로 별도 request payload가 없습니다. 실제 요청 필드는 하위 endpoint 문서를 확인합니다._

#### Response Result

_개요/사용법 문서이므로 별도 response schema가 없습니다. 실제 응답 필드는 하위 endpoint 문서를 확인합니다._

### [Image Translate Submit Translate Task](https://docs.topview.ai/reference/submit-translate-task)
→ 이미지 번역 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/image_translate/single/task/submit` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `fileId` | `string` | Obtain after uploading through the upload interface. (필수; 기본값: 4b129e710f9442f99605d483bac4ebcb) |
| `languages` | `array` | Support：English、Filipino、French、German、Indonesian、Italian、Russian、Spanish、Swahili、Turkish、Vietnamese、Arabic、Hindi、Simplified Chinese (필수; 기본값: ) |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Image Translate Query Translate Task](https://docs.topview.ai/reference/query-translate-task)
→ 이미지 번역 작업 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/image_translate/single/task/query` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | 비동기 작업을 식별하는 ID입니다. Query API 호출 시 사용합니다. (필수; 기본값: 41b24a504d774c2582c44b76cff27234) |
| `needCloudFrontUrl` | `string` | true/false (기본값: false) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.block` | `string` | 가림/방해 요소가 있는지 여부입니다. `0`은 없음, `1`은 있음을 의미합니다. |
| `result.width` | `string` | 너비입니다. |
| `result.height` | `string` | 높이입니다. |
| `result.format` | `string` | 이미지 형식입니다. |
| `result.content` | `array` | 번역 콘텐츠입니다. |
| `result.content[].type` | `string` | 대상 언어입니다. |
| `result.content[].translationList` | `array` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. |
| `result.content[].translationList[].translation` | `string` | 번역된 텍스트입니다. |
| `result.content[].translationList[].origin` | `string` | Origin text |

### [Image Translate Submit Single Generate Task](https://docs.topview.ai/reference/submit-single-generate-task)
→ 단일 번역 이미지 생성 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/image_generate/single/task/submit` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | The taskId returned by the Submit Translate Task interface (필수; 기본값: 41b24a504d774c2582c44b76cff27234) |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Image Translate Submit Batch Generate Task](https://docs.topview.ai/reference/submit-batch-generate-task)
→ 배치 번역 이미지 생성 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/image_translate/batch/task/submit` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `fileIds` | `array` | Obtain after uploading through the upload interface. (필수) |
| `languages` | `array` | Support：English、Filipino、French、German、Indonesian、Italian、Russian、Spanish、Swahili、Turkish、Vietnamese、Arabic、Hindi、Simplified Chinese (필수) |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Image Translate Query Generate Task](https://docs.topview.ai/reference/query-generate-task)
→ 번역 이미지 생성 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/image_generate/task/query` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | 비동기 작업을 식별하는 ID입니다. Query API 호출 시 사용합니다. (필수; 기본값: 017bf27f7bfa4f8c90f4b1d37ff9f8d5) |
| `needCloudFrontUrl` | `string` | true/false (기본값: false) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.mode` | `string` | BATCH/SINGLE |
| `result.costCredit` | `string` | 차감된 크레딧입니다. |
| `result.details` | `array` | 생성된 이미지 정보입니다. |
| `result.details[].fileId` | `string` | fileId입니다. |
| `result.details[].fileName` | `string` | 파일 이름입니다. |
| `result.details[].format` | `string` | 파일 포맷 또는 확장자입니다. |
| `result.details[].language` | `string` | 생성 이미지의 언어입니다. |
| `result.details[].finishedImageUrl` | `string` | 생성된 이미지 URL입니다. |
| `result.details[].status` | `string` | 상태값입니다. |
| `result.details[].errorMsg` | `string` | 오류 메시지입니다. |

## Video 관련

### [Video Character Swap API Usage](https://docs.topview.ai/reference/video-character-swap-api-usage)
→ 비디오 속 인물을 다른 인물로 교체하는 API 개요

#### 개요

- 비디오 속 인물 또는 캐릭터를 참조 인물로 교체하는 기능의 사용 흐름입니다.
- 실제 호출은 `Video Character Swap Submit Task`와 `Video Character Swap Query Task` 섹션을 기준으로 확인합니다.

#### Request Payload / Query

_개요/사용법 문서이므로 별도 request payload가 없습니다. 실제 요청 필드는 하위 endpoint 문서를 확인합니다._

#### Response Result

_개요/사용법 문서이므로 별도 response schema가 없습니다. 실제 응답 필드는 하위 endpoint 문서를 확인합니다._

### [Video Character Swap Submit Task](https://docs.topview.ai/reference/video_character_swap_submit_task)
→ 비디오 인물 교체 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/video_character_swap/task/submit` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `modelImageFileId` | `string` | Model image file ID (필수) |
| `videoMaskDrawingTaskId` | `string` | The third step of video mask drawing task ID (필수) |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 작업 상태입니다. 보통 `init`, `running`, `success`, `fail` 중 하나입니다. (필수; enum: success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Video Character Swap Query Task](https://docs.topview.ai/reference/video_character_swap_query_task)
→ 비디오 인물 교체 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/video_character_swap/task/query` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `string` | 결과 URL을 CloudFront URL 우선으로 받을지 여부입니다. (enum: true, false) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.costCredit` | `string` | 차감된 크레딧입니다. |
| `result.outputVideoUrl` | `string` | 생성된 비디오 결과 URL입니다. (필수) |

### [Video Process Submit Task](https://docs.topview.ai/reference/video_process_submit_task)
→ 비디오 처리 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/video_process/task/submit` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `inputVideoFileId` | `string` | 원본 비디오 file ID입니다. 지원 형식은 MP4, MOV, AVI이며, 파일 크기는 200MB 이하이고 길이는 최대 60초입니다. (필수) |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 작업 상태입니다. 보통 `init`, `running`, `success`, `fail` 중 하나입니다. (필수; enum: success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Video Process Query Task](https://docs.topview.ai/reference/video_process_query_task)
→ 비디오 처리 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/video_process/task/query` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `string` | 결과 URL을 CloudFront URL 우선으로 받을지 여부입니다. (enum: true, false) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 작업 상태입니다. 보통 `init`, `running`, `success`, `fail` 중 하나입니다. (필수; enum: success, fail, running) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.resizedVideoUrl` | `string` | 리사이즈 처리된 비디오 URL입니다. |
| `result.fps` | `number` | 초당 프레임 수입니다. |
| `result.frames` | `integer` | 비디오의 전체 프레임 수입니다. |
| `result.duration` | `integer` | 길이입니다. |
| `result.format` | `string` | 파일 포맷 또는 확장자입니다. |
| `result.resizedSize` | `array` | 스케일링된 비디오 크기입니다. `[width, height]` 형식입니다. |

### [Video Mask Drawing Submit Task](https://docs.topview.ai/reference/video_mask_drawing_submit_task)
→ 비디오 마스크 드로잉 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/video_tracking/task/submit` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `videoProcessTaskId` | `string` | The video processing task ID obtained in the first step. (필수) |
| `inputInfos` | `array` | 여러 입력 파일 또는 입력 리소스 정보 목록입니다. (필수) |
| `inputInfos[].index` | `string` | The index of the selected frame (필수) |
| `inputInfos[].modifyPoints` | `array` | Modify regional point information Note: modifyPoints and protectPoints cannot be empty at the same time. 예시: "modifyPoints": [ [ 624.2696629213483, 627.2320675105485 ], [ 624.2696629213483, 699.915611814346 ] ] |
| `inputInfos[].protectPoints` | `array` | Protection area location information. 예시: "protectPoints": [ [ 233.25842696629212, 411.873417721519 ] ] |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 작업 상태입니다. 보통 `init`, `running`, `success`, `fail` 중 하나입니다. (필수; enum: success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Video Mask Drawing Query Task](https://docs.topview.ai/reference/video_mask_drawing_query_task)
→ 비디오 마스크 드로잉 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/video_tracking/task/query` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `string` | 결과 URL을 CloudFront URL 우선으로 받을지 여부입니다. (enum: true, false) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.costCredit` | `string` | 차감된 크레딧입니다. |
| `result.maskVideoPath` | `string` | The mask video path of the modify area (필수) |
| `result.protectMaskVideoPath` | `string` | The mask video path of the protected area (필수) |
| `result.trackingVideoPath` | `string` | Preview video after masking (필수) |

## Avatar 관련

### [Photo Avatar / Avatar4](https://docs.topview.ai/reference/avatar-4)
→ 최신 Photo Avatar/Avatar4 모델 개요

#### 개요

- Photo Avatar/Avatar4 기능의 개요 문서입니다.
- 텍스트 기반 TTS 또는 업로드 오디오를 사용해 사진 아바타 영상을 생성합니다.
- 실제 호출 필드는 `Photo Avatar Submit Task`, `Photo Avatar Query Task`, Avatar 목록/카테고리/Custom Avatar API 섹션을 기준으로 확인합니다.

#### Request Payload / Query

_개요/사용법 문서이므로 별도 request payload가 없습니다. 실제 요청 필드는 하위 endpoint 문서를 확인합니다._

#### Response Result

_개요/사용법 문서이므로 별도 response schema가 없습니다. 실제 응답 필드는 하위 endpoint 문서를 확인합니다._

### [Photo Avatar List](https://docs.topview.ai/reference/photo_avatar_list)
→ 사용 가능한 포토 아바타 목록 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/photo_avatar/template/list` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `categories` | `string` | Only `isCustom=false`, the categories are valid. Separate multiple categories with ",". Retrieve values from the interface **[Avatar Category List](https://docs.topview.ai/update/reference/get_new-endpoint-54#/)** (기본값: 4ac8d232aa1945efaedf67b56ddf371d,b8e64f37db1c48a2aea904f3a2ae4522) |
| `isCustom` | `boolean` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (enum: True, False) |
| `pageNo` | `string` | 페이지 번호입니다. |
| `pageSize` | `string` | 페이지당 항목 수입니다. |

#### Response Result

_없음 또는 원문 문서에 명시되지 않음_

### [Avatar Category List](https://docs.topview.ai/reference/avatar_category_list)
→ 아바타 카테고리 목록 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/photo_avatar/template/category` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `categoryId` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. |
| `pageNo` | `string` | 페이지 번호입니다. |
| `pageSize` | `string` | 페이지당 항목 수입니다. |

#### Response Result

_없음 또는 원문 문서에 명시되지 않음_

### [Create Custom Avatar](https://docs.topview.ai/reference/create_custom_avatar)
→ 사용자 지정 아바타 생성

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/photo_avatar/template/create` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `templateImageFileId` | `string` | Upload local file to S3. **[How to upload file to s3 and get fileId](https://docs.topview.ai/reference/upload-api-usage#/)** (필수; 기본값: 2a22d8f1364d4903a4f7ec98d2046899) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `String` | 응답 코드입니다. |
| `message` | `String` | 응답 메시지입니다. |
| `result` | `String` | 결과 객체입니다. |

### [Delete Custom Avatar](https://docs.topview.ai/reference/delete_custom_avatar)
→ 사용자 지정 아바타 삭제

#### Endpoint

| Method | URL |
|---|---|
| `DELETE` | `https://api.topview.ai/v1/photo_avatar/template/delete` |

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: ) |
| `Authorization` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `aiAvatarId` | `string` | Retrieve values from the interfaces **[Avatar List](https://docs.topview.ai/reference/get_new-endpoint-53#/)** or **[Create Custom Avatar](https://docs.topview.ai/reference/get_new-endpoint-55#/)** (필수; 기본값: 45b3819812034602b7395eea075b6cf4) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `String` | 응답 코드입니다. |
| `message` | `String` | 응답 메시지입니다. |
| `result` | `Null` | 결과 객체입니다. |

### [Photo Avatar Submit Task](https://docs.topview.ai/reference/avatar4_submit_task)
→ Photo Avatar 비동기 영상 생성 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/photo_avatar/task/submit` |

#### 개요

- Photo Avatar task를 비동기로 제출합니다.
- 제출 성공 시 query endpoint에서 사용할 `taskId`를 반환합니다.
- `scriptMode=text`는 TTS 방식이며 `ttsText`, `voiceId`가 필요합니다. `voiceModel`, `voiceSettings`는 선택입니다.
- `scriptMode=audio`는 업로드 오디오 파일을 사용하며 `audioFileId`가 필요합니다.
- 과금 유형은 `photo_avatar4`이며, 생성 영상 길이를 초 단위로 올림해 차감합니다.
- 단가: `avatar4`=0.1/s, `avatar4Fast`=0.06/s, `avatar2`=0.4/s.
- `offPeak=true`이고 `mode=avatar4/avatar4Fast`이면 50% 할인됩니다. 성공 시 정산되고 실패 시 환불됩니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수; 기본값: ) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer {apiKey}` 형식입니다. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `avatarId` | `string` | Avatar template ID입니다. public/custom avatar 모두 가능하며, avatar 지정 시 `avatarId` 또는 `templateImageFileId` 중 하나를 전달합니다. |
| `templateImageFileId` | `string` | 업로드된 모델 이미지의 fileId입니다. upload endpoint에서 얻으며 `avatarId` 대신 사용할 수 있습니다. |
| `mode` | `string` | 생성 모드입니다. `avatar2`, `avatar4`, `avatar4Fast` 중 하나입니다. (enum: avatar2, avatar4, avatar4Fast) |
| `scriptMode` | `string` | 스크립트 소스입니다. `text`는 TTS를 사용하며 `ttsText`, `voiceId`가 필요합니다. `audio`는 업로드 오디오 파일을 사용하며 `audioFileId`가 필요합니다. (enum: text, audio) |
| `ttsText` | `string` | 보이스오버 스크립트입니다. `scriptMode=text`일 때 필수입니다. |
| `voiceId` | `string` | Voice ID입니다. `scriptMode=text`일 때 필수입니다. |
| `voiceModel` | `string` | `scriptMode=text`일 때 사용할 음성 모델입니다. 생략하면 백엔드 기본 모델을 사용합니다. 지원 값: `elevenlabs-v2.5`, `elevenlabs-v3`, `minimax-v2.5`. |
| `voiceSettings` | `object` | 고급 음성 설정입니다. `scriptMode=text`이고 `voiceModel`이 제공된 경우에만 적용됩니다. |
| `voiceSettings.volume` | `integer` | 볼륨입니다. 범위는 0~200이며, 범위를 벗어나면 `4000` 오류를 반환합니다. |
| `voiceSettings.stability` | `integer` | 안정성 설정입니다. 범위는 0~100이며, 범위를 벗어나면 `4000` 오류를 반환합니다. |
| `audioFileId` | `string` | upload endpoint에서 얻은 오디오 fileId입니다. `scriptMode=audio`일 때 필수입니다. |
| `captionId` | `string` | Caption ID (선택) |
| `customMotion` | `string` | Motion/expression prompt (선택), max length 600 |
| `saveCustomAiAvatar` | `string` | custom avatar로 저장할지 여부입니다. `true`/`false` 값을 사용합니다. |
| `offPeak` | `boolean` | Whether this is an off-peak task (선택), effective only when mode=avatar4/avatar4Fast. true means off-peak: 50% credits, fallback timeout 24 hours |
| `boardId` | `string` | 연결할 Board ID입니다. (선택). 전달하면 시스템이 `aigc-backend`에 대응되는 `boardTask`를 생성하며, 응답에 `boardId`와 `boardTaskIds`가 반환될 수 있습니다. |
| `noticeUrl` | `string` | task 완료 시 호출되는 callback URL입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | Message |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 제출 직후 반환되는 접수 상태입니다. 실제 실행 상태(`init`/`running`/`success`/`fail`)는 query endpoint로 확인합니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.subTaskId` | `string` | 일부 시나리오에서 반환되는 Sub-task ID입니다. 일반적으로는 이 endpoint에서 반환되지 않습니다. |
| `result.boardTaskIds` | `array` | List of Board sub-task IDs returned Board와 연결된 경우 |
| `result.boardId` | `string` | Board ID입니다. |
| `result.noticeUuid` | `string` | 비동기 callback 알림 UUID입니다. request body에 `noticeUrl`이 설정된 경우 반환됩니다. |

### [Photo Avatar Query Task](https://docs.topview.ai/reference/avatar4_query_task)
→ Photo Avatar 작업 상태 및 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/photo_avatar/task/query` |

#### 개요

Photo Avatar 작업 상태와 결과를 `taskId`로 조회합니다.

- 권장 polling 주기는 3~5초이며, timeout 기준은 약 5분입니다.
- `status`는 `init`, `running`, `success`, `fail` 중 하나입니다.
- 실패 시 `errorMsg`에 원인이 들어가며, 성공 시 `costCredit`이 확정됩니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수; 기본값: ) |
| `Authorization` | `string` | 인증용 API Key입니다. `Bearer {apiKey}` 형식입니다. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `boolean` | CloudFront 접근 URL을 반환할지 여부입니다. 기본값은 `false`입니다. (기본값: False) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | Message |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.costCredit` | `number` | 차감된 크레딧입니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.boardTaskId` | `string` | task가 Board에서 온 경우 반환되는 Board Task ID입니다. |
| `result.finishedVideoCoverUrl` | `string` | 생성된 비디오의 커버 URL입니다. |
| `result.finishedVideoUrl` | `string` | 생성된 비디오 URL입니다. 오디오를 포함합니다. |
| `result.aiAvatar` | `object` | Avatar 상세 정보입니다. |
| `result.aiAvatar.aiavatarId` | `string` | Avatar ID |
| `result.aiAvatar.aiavatarName` | `string` | Avatar name |
| `result.aiAvatar.gender` | `string` | Gender |
| `result.aiAvatar.coverUrl` | `string` | Avatar 커버 URL입니다. |
| `result.aiAvatar.previewVideoUrl` | `string` | Avatar preview video URL입니다. |
| `result.aiAvatar.ethnicities` | `array` | Avatar ethnicity 정보 목록입니다. |
| `result.aiAvatar.voiceoverIdDefault` | `string` | 기본 Voice ID입니다. |
| `result.aiAvatar.faceSquareConfig` | `object` | 얼굴 bounding box 설정입니다. |
| `result.aiAvatar.type` | `integer` | Avatar 유형입니다. `0`=traditional avatar, `1`=zero-shot avatar, `2`=photo avatar입니다. (enum: 0, 1, 2) |

### [Avatar Marketing Video Getting Started](https://docs.topview.ai/reference/getting-started-1)
→ Avatar Marketing Video API 개요

#### 개요

- 상품 URL 또는 로컬 이미지/비디오 소재를 입력해 마케팅 영상을 생성하는 Avatar Marketing Video 개요 문서입니다.
- 작업 생성, 조회, 스크립트 수정, export가 분리되어 있으므로 `Submit -> Query -> Script 수정 -> Export` 흐름으로 이해하는 것이 좋습니다.

#### Request Payload / Query

_개요/사용법 문서이므로 별도 request payload가 없습니다. 실제 요청 필드는 하위 endpoint 문서를 확인합니다._

#### Response Result

_개요/사용법 문서이므로 별도 response schema가 없습니다. 실제 응답 필드는 하위 endpoint 문서를 확인합니다._

### [Avatar Marketing Video Submit Task](https://docs.topview.ai/reference/avatar_marketing_video_submit_task)
→ 마케팅 영상 생성 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/m2v/task/submit` |

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `productLink` | `string` | 상품 페이지 URL입니다. `productLink`와 `fileIds` 중 하나는 필요합니다. 둘 다 전달하면 `productLink`에서 파싱된 이미지/비디오가 우선 사용됩니다. |
| `fileIds` | `array` | 업로드 API로 얻은 이미지/비디오 `fileId` 목록입니다. `productLink`와 함께 전달하면 `productLink`가 우선됩니다. |
| `productName` | `string` | 상품 링크에서 자동 파싱될 수 있습니다. `productLink`가 비어 있을 때 필수입니다. |
| `productDescription` | `string` | 상품 링크에서 자동 파싱될 수 있습니다. `productLink`가 비어 있고 `isDiyScript=false`일 때 필수입니다. |
| `aspectRatio` | `string` | 기본값: 9:16. Optional values: 9:16, 3:4, 1:1, 4:3, 16:9 (enum: 9:16, 3:4, 1:1, 4:3, 16:9) |
| `language` | `string` | 번역/생성에 사용할 언어 코드입니다. 기본값은 `en`이며, `ko`, `ja`, `zh-CN`, `zh-Hant`, `en` 등 다수 언어를 지원합니다. 전체 지원 언어와 Thai voiceId 제한은 공식 문서의 enum을 확인하세요. |
| `voiceId` | `string` | Obtain it through the **[voice query interface](https://docs.topview.ai/reference/voice_query)**. (기본값: ) |
| `captionId` | `string` | **caption list interface**를 통해 얻는 caption ID입니다. 지정하지 않으면 무작위로 선택됩니다. |
| `aiavatarId` | `string` | **aiAvatar query interface**를 통해 얻는 aiAvatar ID입니다. |
| `videoLengthType` | `integer` | 기본값: 1, optional values: 1, 2, 3, 4. 1： 30-50s 2： 15-30s 3： 30-45s 4： 45-60s (enum: 1, 2, 3, 4) |
| `endcardFileId` | `string` | **file upload interface**로 업로드한 뒤 얻는 fileId입니다. |
| `endcardAspectRatio` | `string` | 기본값: all. Optional values: all, 9:16, 3:4, 1:1, 4:3, 16:9 (enum: all, 9:16, 3:4, 1:1, 4:3, 16:9) |
| `endcardBackgroundColor` | `string` | 기본값: black. Optional values: black, white |
| `logoFileId` | `string` | **file upload interface**로 업로드한 뒤 얻는 fileId입니다. |
| `preview` | `string` | 기본값: false. When set to true, a preview video will be returned first. When you need to export the video, you need to trigger the export interface for export. When set to false or empty, an exported video will be returned. |
| `isDiyScript` | `string` | 사용자 지정 스크립트를 사용할지 여부입니다. `true`이면 custom script를 사용하며 기본값은 `false`입니다. |
| `diyScriptDescription` | `string` | 사용자 지정 스크립트 내용입니다. `isDiyScript=true`일 때만 적용됩니다. |
| `diyStyle` | `string` | 스크립트 스타일 유형입니다. `diyScriptStyle`은 preset style, `diyScriptPrompt`는 custom prompt를 사용합니다. `isDiyScript=true`일 때만 적용됩니다. (enum: diyScriptStyle, diyScriptPrompt) |
| `diyScriptStyle` | `string` | `diyStyle=diyScriptStyle`일 때 사용할 사전 정의 스크립트 스타일입니다. Pain point, curiosity hook, storytelling, brand-oriented, AI auto-select 등 다양한 스타일 enum을 지원합니다. 전체 enum은 공식 문서를 확인하세요. |
| `diyScriptPrompt` | `string` | 사용자 지정 스크립트 스타일 설명입니다. `diyStyle=diyScriptPrompt`일 때 값을 전달해야 합니다. |
| `targetAudience` | `string` | 타깃 오디언스 정보입니다. AI가 스크립트를 작성할 때 방향성을 잡는 데 사용합니다. |
| `musicFileId` | `string` | 사용자 지정 배경음악 ID입니다. file upload interface로 업로드한 뒤 얻습니다. 비어 있으면 무작위 음악을 사용합니다. |
| `noticeUrl` | `string` | `noticeUrl` 관련 상세 규칙은 공식 callback 문서를 참고하세요. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.subTaskId` | `string` | 하위 작업 ID입니다. 일부 복합 작업에서 반환됩니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Avatar Marketing Video Query Task](https://docs.topview.ai/reference/get_m2v-task-query)
→ 마케팅 영상 생성 작업 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/query` |

#### 개요

M2V task 상태와 결과를 조회합니다.

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `boolean` | 결과 URL을 CloudFront URL 우선으로 받을지 여부입니다. (기본값: false) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, fail, success) |
| `result.productName` | `string` | 비디오와 연결된 상품 이름입니다. |
| `result.productDescription` | `string` | 상품 특징과 사양에 대한 상세 설명입니다. |
| `result.exportVideos` | `array` | 내보내기 결과 비디오 목록입니다. |
| `result.exportVideos[].scriptId` | `number` | export된 비디오와 연결된 script ID입니다. |
| `result.exportVideos[].status` | `string` | 상태값입니다. (enum: init, running, success, fail, null) |
| `result.exportVideos[].title` | `string` | export된 비디오 제목입니다. |
| `result.exportVideos[].description` | `string` | export된 비디오 설명입니다. |
| `result.exportVideos[].videoUrl` | `string` | export된 비디오 파일 URL입니다. |
| `result.exportVideos[].coverUrl` | `string` | 비디오 커버 이미지 URL입니다. |
| `result.exportVideos[].videoDuration` | `number` | 비디오 길이입니다. 단위는 milliseconds입니다. |
| `result.exportVideos[].videoEtag` | `string` | 비디오 파일의 ETag입니다. 캐시 검증에 사용됩니다. |
| `result.previewVideos` | `array` | 미리보기 비디오 목록입니다. |
| `result.previewVideos[].scriptId` | `number` | preview video와 연결된 script ID입니다. |
| `result.previewVideos[].status` | `string` | 상태값입니다. (enum: init, running, success, fail, null) |
| `result.previewVideos[].title` | `string` | export된 비디오 제목입니다. |
| `result.previewVideos[].description` | `string` | preview video 설명입니다. |
| `result.previewVideos[].videoUrl` | `string` | preview video 파일 URL입니다. |
| `result.previewVideos[].coverUrl` | `string` | preview video 커버 이미지 URL입니다. |
| `result.previewVideos[].videoDuration` | `number` | preview video 길이입니다. 단위는 milliseconds입니다. |
| `result.previewVideos[].videoEtag` | `string` | preview video 파일의 ETag입니다. 캐시 검증에 사용됩니다. |
| `result.videoSceneInfoList` | `array` | 비디오 장면 상세 정보를 담은 scene 정보 객체 배열입니다. |
| `result.videoSceneInfoList[].mediaId` | `string` | 장면의 media ID입니다. |
| `result.videoSceneInfoList[].seq` | `number` | 장면 순서 번호입니다. |
| `result.videoSceneInfoList[].mediaType` | `string` | 미디어 유형입니다. video 또는 image입니다. |
| `result.videoSceneInfoList[].sceneAnalyseDesc` | `string` | 장면 분석 설명입니다. |
| `result.videoSceneInfoList[].sceneFileName` | `string` | 장면 비디오 파일 이름입니다. |
| `result.videoSceneInfoList[].videoSceneCoverUrl` | `string` | 장면 비디오 커버 이미지 URL입니다. |
| `result.videoSceneInfoList[].start` | `number` | 장면 시작 시간입니다. 단위는 초입니다. |
| `result.videoSceneInfoList[].end` | `number` | 장면 종료 시간입니다. 단위는 초입니다. |
| `result.imageSceneInfoList` | `array` | 이미지 장면 상세 정보를 담은 scene 정보 객체 배열입니다. |
| `result.imageSceneInfoList[].mediaId` | `string` | 이미지 장면의 media ID입니다. |
| `result.imageSceneInfoList[].mediaType` | `string` | 미디어 유형입니다. image입니다. |
| `result.imageSceneInfoList[].sceneAnalyseDesc` | `string` | 이미지 장면 분석 설명입니다. |
| `result.imageSceneInfoList[].sceneFileName` | `string` | 이미지 장면 파일 이름입니다. |
| `result.imageSceneInfoList[].imageSceneUrl` | `string` | 이미지 장면 URL입니다. |
| `result.scriptContentList` | `array` | 스크립트 세그먼트 상세 정보를 담은 script content 객체 배열입니다. |
| `result.scriptContentList[].scriptId` | `number` | script ID입니다. |
| `result.scriptContentList[].scriptContents` | `array` | script content 세그먼트 배열입니다. |
| `result.scriptContentList[].scriptContents[].segId` | `number` | script content의 segment ID입니다. |
| `result.scriptContentList[].scriptContents[].segText` | `string` | script segment 텍스트입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |

### [Avatar Marketing Video Export Task](https://docs.topview.ai/reference/avatar_marketing_video_export_task)
→ 마케팅 영상 내보내기 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/m2v/export` |

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `scriptId` | `string` | 스크립트 식별자입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.noticeUuid` | `string` | 콜백 알림을 식별하는 UUID입니다. `noticeUrl`을 설정한 경우 반환될 수 있습니다. |

### [Avatar Marketing Video List Task](https://docs.topview.ai/reference/avatar_marketing_video_list_task)
→ 마케팅 영상 작업 목록 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/m2v/task/list` |

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `pageNo` | `integer` | 페이지 번호입니다. |
| `pageSize` | `integer` | 페이지당 항목 수입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.productName` | `string` | 상품 이름입니다. |
| `result.productDescription` | `string` | 상품 설명입니다. |
| `result.gmtCreate` | `string` | task 생성 시각입니다. 형식은 `yyyy-MM-dd HH:mm:ss`입니다. |
| `result.diskSpace` | `object` | 사용자 저장 공간 사용량/한도 정보입니다. |
| `result.diskSpace.size` | `string` | task가 차지하는 공간 크기입니다. |
| `result.diskSpace.unit` | `string` | Unit: byte |

### [Avatar Marketing Video Delete Task](https://docs.topview.ai/reference/avatar_marketing_video_delete_task)
→ 마케팅 영상 작업 삭제

#### Endpoint

| Method | URL |
|---|---|
| `DELETE` | `https://api.topview.ai/v1/m2v/task/delete` |

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `taskIds` | `array` | 대상 작업 ID 목록입니다. (필수; 기본값: c8ce2659691f41b38312eda9ef32b152) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: success, fail) |
| `result.errosMsg` | `string` | 오류 메시지입니다. 원문 스키마의 오탈자 필드명으로 보입니다. |
| `result.data` | `object` | API별 상세 결과 데이터가 담기는 객체입니다. |
| `result.data.size` | `integer` | task 크기입니다. 단위는 bytes입니다. |
| `result.data.unit` | `string` | 크기 단위입니다. 예: byte. |

### [Avatar Marketing Video List Script Content](https://docs.topview.ai/reference/avatar_marketing_video_list_script_content)
→ 스크립트 콘텐츠 목록 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/m2v/script/list` |

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.scriptId` | `integer` | 스크립트 식별자입니다. |
| `result.scriptContents` | `array` | 스크립트 세그먼트 또는 장면별 콘텐츠 목록입니다. |
| `result.scriptContents[].segId` | `string` | 스크립트 내 segment 고유 식별자입니다. |
| `result.scriptContents[].segText` | `string` | 스크립트 segment 텍스트입니다. |

### [Avatar Marketing Video Update Script Content](https://docs.topview.ai/reference/avatar_marketing_video_update_script_content)
→ 스크립트 콘텐츠 수정

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/m2v/script/update` |

#### Headers

_없음 또는 원문 문서에 명시되지 않음_

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `scriptId` | `string` | The default value is 0 (representing the first script) (필수; 기본값: 0) |
| `scriptContents` | `array` | 스크립트 세그먼트 또는 장면별 콘텐츠 목록입니다. (필수) |
| `scriptContents[].segId` | `string` | 원문 스키마에 별도 설명이 없습니다. 실제 의미는 해당 API의 예시 응답과 함께 확인하세요. (필수; 기본값: 0) |
| `scriptContents[].segText` | `string` | 예시: This is segText Test (필수; 기본값: ) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. |
| `result.errorMsg` | `string` | 오류 메시지입니다. |

## Video Object Remove 관련

### [SelectionBox Parameter Reference](https://docs.topview.ai/reference/selectionbox-parameter-reference)
→ 비디오 프레임 내 특정 영역을 백분율 좌표로 지정하는 직사각형 파라미터

#### 개요

- `selectionBox`는 비디오 프레임 안의 직사각형 영역을 백분율 좌표로 지정하는 파라미터입니다.
- `x`, `y`, `w`, `h`는 0~1 기준 좌표/크기이며, `mode`에 따라 영역 내부 제거 또는 외부 제거 동작을 지정합니다.
- 실제 사용처는 Video Object Remove Submit API입니다.

#### Request Payload / Query

_개요/사용법 문서이므로 별도 request payload가 없습니다. 실제 요청 필드는 하위 endpoint 문서를 확인합니다._

#### Response Result

_개요/사용법 문서이므로 별도 response schema가 없습니다. 실제 응답 필드는 하위 endpoint 문서를 확인합니다._

### [Video Object Remove Submit Task](https://docs.topview.ai/reference/video-object-remove-submit-task)
→ 비디오 객체 제거 작업 제출

#### Endpoint

| Method | URL |
|---|---|
| `POST` | `https://api.topview.ai/v1/common-task/video-object-remove/submit` |

#### 개요

- Video Object Remove task를 비동기로 제출합니다.
- 제출 성공 시 query API에서 사용할 `taskId`를 반환합니다.
- 크레딧은 완료 후 비디오 길이를 기준으로 차감됩니다.
- 실제 차감량은 query 응답의 `costCredit`에서 확인합니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | 사용자 ID입니다. (필수; 기본값: ) |
| `Authorization` | `string` | 인증용 API Key입니다. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Body Params**

| Property | Type | 의미 |
|---|---|---|
| `mode` | `string` | 제거 모드입니다. `auto`는 전체 프레임 자동 제거, `clear-inside`는 selection box 내부 제거, `clear-outside`는 selection box 외부 제거입니다. |
| `videoFileId` | `string` | upload API에서 반환된 비디오 fileId입니다. video 타입이어야 하며 지원되는 비디오 형식을 사용해야 합니다. (필수) |
| `selectionBox` | `object` | 비디오 너비/높이 기준 백분율 selection 영역입니다. `clear-inside`, `clear-outside` 모드에서는 필수이며, `auto` 모드에서는 생략할 수 있습니다. |
| `selectionBox.x` | `number` | 좌상단 x 좌표입니다. 단위는 percent이며 0~100 범위입니다. (필수) |
| `selectionBox.y` | `number` | 좌상단 y 좌표입니다. 단위는 percent이며 0~100 범위입니다. (필수) |
| `selectionBox.w` | `number` | 너비입니다. 단위는 percent이며 0~100 범위이고, 0보다 커야 합니다. (필수) |
| `selectionBox.h` | `number` | 높이입니다. 단위는 percent이며 0~100 범위이고, 0보다 커야 합니다. (필수) |
| `noticeUrl` | `string` | task 완료 시 호출되는 선택 webhook URL입니다. 공식 callback 문서를 참고하세요. |
| `boardId` | `string` | Board ID입니다. |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | submit 직후 반환되는 접수 상태입니다. task가 접수되었음을 의미하며, 실제 실행 상태는 query endpoint로 polling해야 합니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.boardTaskIds` | `array` | Board와 연결된 경우 반환되는 board task ID 목록입니다. |
| `result.boardId` | `string` | Board ID입니다. |
| `result.noticeUuid` | `string` | 비동기 callback 추적용 notification UUID입니다. |

### [Video Object Remove Query Task](https://docs.topview.ai/reference/video-object-remove-query-task)
→ 비디오 객체 제거 작업 결과 조회

#### Endpoint

| Method | URL |
|---|---|
| `GET` | `https://api.topview.ai/v1/common-task/video-object-remove/query` |

#### 개요

- `taskId`로 Video Object Remove task 상태와 산출물을 조회합니다.
- task가 성공하기 전까지 출력 필드는 비어 있습니다.
- 성공 시 출력 비디오 URL, cover URL, fileId, 크기 정보를 반환합니다.

#### Headers

| Property | Type | 의미 |
|---|---|---|
| `Topview-Uid` | `string` | User ID, must match the task owner (필수; 기본값: ) |
| `Authorization` | `string` | 인증용 API Key입니다. (필수; 기본값: Bearer ) |

#### Request Payload / Query

**Query Params**

| Property | Type | 의미 |
|---|---|---|
| `taskId` | `string` | task ID입니다. |
| `needCloudFrontUrl` | `boolean` | `true`이면 outputVideoUrl and outputCoverUrl prefer CloudFront URLs for acceleration. Default false. (기본값: False) |

#### Response Result

| Property | Type | 의미 |
|---|---|---|
| `code` | `string` | 응답 코드입니다. |
| `message` | `string` | 응답 메시지입니다. |
| `result` | `object` | 결과 객체입니다. |
| `result.taskId` | `string` | task ID입니다. |
| `result.status` | `string` | 상태값입니다. (enum: init, running, success, fail) |
| `result.errorMsg` | `string` | 오류 메시지입니다. |
| `result.costCredit` | `number` | Credits actually consumed by this task. Default 0.000. Charged after success based on the input video duration. (기본값: 0) |
| `result.boardId` | `string` | Board ID입니다. |
| `result.outputVideoUrl` | `string` | task 성공 후 반환되는 출력 비디오 URL입니다. `needCloudFrontUrl=true`이면 CloudFront URL을 우선 반환합니다. |
| `result.outputCoverUrl` | `string` | task 성공 후 반환되는 출력 커버 이미지 URL입니다. `needCloudFrontUrl=true`이면 CloudFront URL을 우선 반환합니다. |
| `result.outputVideoFileId` | `string` | task 성공 후 반환되는 출력 비디오 fileId입니다. 후속 API의 입력 fileId로 재사용할 수 있습니다. |
| `result.outputCoverFileId` | `string` | task 성공 후 반환되는 출력 커버 fileId입니다. |
| `result.outputWidth` | `integer` | task 성공 후 반환되는 출력 비디오 너비입니다. 단위는 pixels입니다. |
| `result.outputHeight` | `integer` | task 성공 후 반환되는 출력 비디오 높이입니다. 단위는 pixels입니다. |
| `result.outputDuration` | `integer` | task 성공 후 반환되는 출력 비디오 길이입니다. 단위는 milliseconds입니다. |

# 요약

- **Credit/Logs/Space**: 계정 크레딧, 차감 로그, 저장소 사용량 확인
- **Boards**: 보드/작업 목록 조회, 상세 조회, 삭제, ZIP 다운로드 요청 및 상태 확인
- **Storyboard & AI Music**: 비동기 콘텐츠 생성 작업 제출 후 `taskId`로 결과 조회
- **Voice Clone**: 참조 오디오와 텍스트로 음성 복제, 결과는 오디오 fileId/URL 중심
- **Image/Video**: 인물 교체, 마스크 드로잉, 번역, 비디오 처리/객체 제거
- **Avatar**: Photo Avatar/Avatar4와 Avatar Marketing Video 작업 생성·조회·내보내기·스크립트 관리
- **SelectionBox**: 비디오 프레임 내 영역을 `x`, `y`, `w`, `h`, `mode`로 지정
