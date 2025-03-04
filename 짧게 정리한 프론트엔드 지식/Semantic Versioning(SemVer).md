#### 1. Semantic Versioning(SemVer)

> **정의**
- Semantic Versioning은 소프트웨어 버전 번호를 명확하고 일관되게 관리하기 위한 규칙이다. 
	- 이 규칙은 개발자들이 소프트웨어의 변경 사항을 추적하고, 사용자가 소프트웨어를 사용할 때 **호환성 문제**를 피할 수 있도록 돕는다.

> **형식**
```null
MAJOR.MINOR.PATCH
```
- 패키지 버전은 보통 `MAJOR.MINOR.PATCH` 형식으로 표시된다.
	- **`MAJOR(메이저 - 주요버전)`
		- **호환되지 않는 API 변경**이 있을 때 증가한다.
	    - 새로운 주요 버전이 출시되면, 이전 버전과 호환되지 않거나 호환성 문제가 발생할 수 있다.
	    - 예: 버전 `2.0.0`에서 `3.0.0`으로 변경되면, 기능이나 API에 **큰 변화**가 있다는 의미이다.
	
	- **`MINOR(마이너 - 부가버전)`
		- **하위 버전과 호환이 가능한 새로운 기능 추가**가 있을 때 증가한다.
	    - 예를 들어, 새로운 기능이 추가되지만 기존 기능과 호환성은 그대로 유지되는 경우이다.
	    - 예: 버전 `2.1.0`은 `2.0.0`과 호환되며, 새로운 기능을 추가한 것이다
	
	- **`PATCH(패치 - 패치버전)`
	    - **버그 수정**과 같은 **호환성 문제가 없는 변경**이 있을 때 증가한다.
	    - 예를 들어, 기존 기능의 버그가 수정되거나 성능이 개선된 경우이다.
	    - 예: 버전 `2.0.1`은 `2.0.0`에서 버그를 수정한 것이다.

> **추가적인 규칙**
- 프리릴리즈 (Pre-release) 버전
    - 프리릴리즈 버전은 `-alpha`, `-beta`, `-rc`와 같은 태그로 표시된다.
    - 이 버전은 기능이 완료되지 않았거나, 아직 검증되지 않았을 때 사용된다.
	    - 예: `1.0.0-alpha`, `1.0.0-beta.1` 등

- 빌드 메타데이터 (Build Metadata)
    - 빌드 메타데이터는 `+` 기호 뒤에 추가되는데, 이는 버전 정보를 더 자세히 나타내기 위해 사용된다.
    - 예: `1.0.0+20130313144700`

---
#### 2. **`^` (Caret)**

- **의미**
	- **최대 MINOR와 PATCH 업데이트까지는 허용한다.**
	- 이 때, MAJOR 버전은 고정된다.

- **예제**
    - `"lodash": "^4.17.0"`  
        → 허용 범위: `>= 4.17.0` AND `< 5.0.0`  
        (즉, `4.17.1`, `4.18.0` 등은 허용, 하지만 `5.0.0` 이상은 허용하지 않음)
    
- **사용 목적**
	- 기본적으로 최신 패치와 기능 업데이트를 허용하면서, **주요 버전(MAJOR) 변경으로 인한 호환성 문제**를 방지

---
#### 3. **`~` (Tilde)**

- **의미**
	- **최대 PATCH 업데이트만 허용한다.**
	- MAJOR와 MINOR는 고정된다.

- **예제**
	- `"lodash": "~4.17.0"`  
        → 허용 범위: `>= 4.17.0` AND `< 4.18.0`  
        (즉, `4.17.1`, `4.17.2` 등은 허용, 하지만 `4.18.0` 이상은 허용하지 않음)

- **사용 목적**
	- **버그 수정만 허용**하면서 안정성을 유지

---
#### 4. **`*` (Wildcard)**

- **의미**
	- 해당 위치의 모든 버전을 허용한다.

- **예제**
	- `"lodash": "4.*"`  
        → 허용 범위: `>= 4.0.0` AND `< 5.0.0`  
        (즉, 모든 `4.x.x` 버전을 허용)
    
    - `"lodash": "*"`  
        → 모든 버전을 허용

- **사용 목적**
	- 제한 없이 최신 버전을 사용하고 싶을 때

---
#### **5. `>=`, `<=`, `<`, `>`**

- **의미**
	- 특정 버전 이상 또는 이하로 제한

- **예제**
    - `"lodash": ">=4.17.0"`  
        → 허용 범위: `4.17.0` 이상 모든 버전
    
	- `"lodash": "<5.0.0"`  
        → 허용 범위: `5.0.0` 미만의 모든 버전

---
#### **6. `x` (or omitted digit)**

- **의미**
	- 해당 위치의 모든 값을 허용
	    - `"4.17.x"`  
	        → `4.17.0`부터 `4.17.*`까지 모든 버전을 허용

- **예제**:
    - `"lodash": "4.x"`  
        → 허용 범위: `4.0.0` 이상 `5.0.0` 미만

---
#### **7. 정확한 버전**

- **의미**
	- 특정 버전만 허용

- **예제**:
    - `"lodash": "4.17.0"`  
        → 정확히 `4.17.0` 버전만 허용

---
#### 8. **비교 표**

| 기호         | 설명                  | 예제           | 허용 범위                      |
| ---------- | ------------------- | ------------ | -------------------------- |
| `^`        | MINOR/PATCH 업데이트 허용 | `"^4.17.0"`  | `>= 4.17.0` AND `< 5.0.0`  |
| `~`        | PATCH 업데이트만 허용      | `"~4.17.0"`  | `>= 4.17.0` AND `< 4.18.0` |
| `*`        | 모든 버전 허용            | `"*"`        | 모든 버전                      |
| `>=`, `<=` | 특정 버전 이상 또는 이하      | `">=4.17.0"` | `4.17.0` 이상                |
| `<`, `>`   | 특정 버전 미만 또는 초과      | `"<5.0.0"`   | `5.0.0` 미만                 |
| `x`        | 해당 자리의 모든 값을 허용     | `"4.x"`      | `4.0.0` 이상 `5.0.0` 미만      |
| 정확한 버전     | 특정 버전만 허용           | `"4.17.0"`   | `4.17.0`                   |

---
#### 9. **권장 사용 사례**

1. **`^`**
    - 기능 추가와 패치 업데이트를 허용하되, 주요 변경으로 인한 충돌 방지
    - 예: `"lodash": "^4.17.0"`

2. **`~`**
    - 안정성이 중요하고, 패치만 허용
    - 예: `"axios": "~1.2.0"`

3. **`*` 또는 `x`**
    - 실험적 프로젝트나 최신 버전 테스트 시
    - 예: `"react": "18.x"`

4. **정확한 버전**
    - 특정 버전에 강하게 의존하는 경우
    - 예: `"webpack": "5.64.4"`

