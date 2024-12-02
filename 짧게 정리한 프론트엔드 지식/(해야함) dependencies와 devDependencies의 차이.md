
### **`dependencies`와 `devDependencies`란?**

Node.js 프로젝트에서 **패키지를 설치**할 때, 해당 패키지가 프로젝트에서 어떻게 사용되는지에 따라 **`dependencies`** 또는 **`devDependencies`**로 구분하여 관리합니다. 이 두 가지는 `package.json` 파일의 중요한 필드이며, 설치한 패키지의 용도를 명확히 구분합니다.

---

### 1. **`dependencies`**

- **정의**: 애플리케이션이 **실행 중**에 반드시 필요한 패키지입니다.
    
- **용도**:
    
    - 코드 실행에 필수적인 라이브러리.
    - 예: React, Express, Lodash 같은 라이브러리.
- **설치 명령어**: 기본적으로 `npm install <패키지>` 명령어로 설치되며, 명시적으로는 `--save` 또는 `-S` 옵션을 사용합니다.
    
    bash
    
    코드 복사
    
    `npm install react --save`
    
- **예시 (`package.json`)**:
    
    json
    
    코드 복사
    
    `"dependencies": {   "express": "^4.17.1",   "react": "^17.0.2" }`
    
- **특징**:
    
    - **프로덕션 환경에서도 설치**됩니다.
    - 실행 시점에 필요하기 때문에 빌드된 애플리케이션에 포함됩니다.

---

### 2. **`devDependencies`**

- **정의**: 애플리케이션을 **개발하거나 빌드하는 과정**에서만 필요한 패키지입니다.
    
- **용도**:
    
    - 코드 검증, 테스트, 빌드, 린트 등 개발 도구.
    - 예: Webpack, Babel, ESLint, Jest 같은 툴.
- **설치 명령어**: `--save-dev` 또는 `-D` 옵션을 사용하여 설치합니다.
    
    bash
    
    코드 복사
    
    `npm install jest --save-dev`
    
- **예시 (`package.json`)**:
    
    json
    
    코드 복사
    
    `"devDependencies": {   "webpack": "^5.64.4",   "eslint": "^8.12.0",   "jest": "^27.4.5" }`
    
- **특징**:
    
    - **프로덕션 환경에서는 설치되지 않음** (`NODE_ENV=production` 설정 시).
    - 개발 도중에만 필요한 도구이므로 최종 애플리케이션 배포 시 포함되지 않습니다.

---

### **비교: `dependencies` vs `devDependencies`**

|**특징**|**dependencies**|**devDependencies**|
|---|---|---|
|**사용 시점**|런타임(애플리케이션 실행 중)|개발 또는 빌드 단계|
|**예시 패키지**|React, Express, Lodash|Webpack, Babel, Jest, ESLint|
|**설치 명령어**|`npm install <패키지>`|`npm install <패키지> --save-dev`|
|**프로덕션 환경**|설치됨|설치되지 않음|
|**최종 빌드 포함 여부**|포함|포함되지 않음|

---

### **실제 사용 예시**

#### `dependencies`에 설치할 패키지

- **React 애플리케이션**에서 사용자 인터페이스를 구성하기 위해 React 패키지가 필요합니다.
    
    bash
    
    코드 복사
    
    `npm install react --save`
    

#### `devDependencies`에 설치할 패키지

- 코드를 린트하고, 테스트하며, 번들링하는 도구는 프로덕션에서는 필요하지 않습니다.
    
    bash
    
    코드 복사
    
    `npm install eslint jest webpack --save-dev`
    

---

### **배포 시 주의 사항**

배포 환경에서는 **`dependencies`**만 설치하는 것이 일반적입니다. 이를 위해 아래와 같이 `NODE_ENV`를 설정합니다.

bash

코드 복사

`npm install --production`

또는 **yarn**을 사용할 경우:

bash

코드 복사

`yarn install --production`

이 명령어는 **`devDependencies`**를 제외하고 **`dependencies`**만 설치합니다.

---

### **결론**

- `dependencies`: 애플리케이션 실행 중 필요한 필수 라이브러리.
- `devDependencies`: 개발 및 빌드 과정에서만 필요한 도구.  
    이렇게 구분하여 관리하면, 최종 프로덕션 환경에 불필요한 패키지가 포함되는 것을 방지할 수 있습니다.
    