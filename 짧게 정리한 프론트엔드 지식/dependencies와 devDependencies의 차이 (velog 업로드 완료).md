###### **Overview**
- Node.js 프로젝트에서는 **패키지를 설치**할 때, 해당 패키지가 프로젝트에서 어떻게 사용되는지에 따라 **`dependencies`** 또는 **`devDependencies`** 로 구분하여 관리한다.
- 이 두 가지는 `package.json` 파일의 중요한 필드이며, 설치한 패키지의 용도를 명확히 구분하는 데에 사용된다.

---
### 1. `dependencies`
###### ※ 정의
- production 환경에서 애플리케이션 실행 시 필수적인 패키지이다.
###### ※ 특징
- 런타임에 필요한 라이브러리
	- 예: 웹 서버용 라이브러리, 프론트엔드 UI 라이브러리
	
- 배포 환경에서 `dependencies`에 명시된 패키지들은 반드시 설치된다.
###### ※ 설치 방법
```bash
npm install <패키지>
```
- 혹은 명시적으로 `--save` 옵션을 사용
```bash
npm install <패키지> --save 
```
###### ※ 예시파일 : `package.json`
- 프로젝트 'project-name'을 실행하기 위해 필요한 패키지
```json
{
  "name": "project-name",
   // ...
  "dependencies": {
    "@ckeditor/ckeditor5-react": "8.0.0",
    "@emotion/react": "11.11.4",
    "@emotion/styled": "11.11.5",
    "@mui/icons-material": "5.15.20",
    "@mui/material": "5.15.20",
    "@mui/styles": "^5.15.20",
    "@mui/x-data-grid": "^7.19.0",
    "@react-pdf/renderer": "3.4.4",
    "@tanstack/react-query": "5.48.0",
    "@tomickigrzegorz/react-circular-progress-bar": "1.1.2",
    "@types/leaflet": "1.9.12",
    "@types/navermaps": "3.7.5",
    "@types/proj4": "2.5.5",
    "@types/react-leaflet": "3.0.0",
    "apexcharts": "3.49.2",
    "axios": "1.7.2",
    "chart.js": "4.4.3",
    "chartjs-plugin-datalabels": "2.2.0",
    "ckeditor5": "42.0.0",
    "echarts": "5.5.0",
    "echarts-for-react": "3.0.2",
    "exceljs": "4.4.0",
    "form-data": "4.0.0",
    "formidable": "2.1.2",
    "framer-motion": "11.2.12",
    "geolib": "3.3.4",
    "html2canvas": "1.4.1",
    "jspdf": "2.5.1",
    "jwt-decode": "^4.0.0",
    "leaflet": "1.9.4",
    "leaflet-draw": "1.0.4",
    "leaflet-easyprint": "2.1.9",
    "lodash": "^4.17.21",
    "moment": "2.30.1",
    "next": "14.2.3",
    "next-auth": "4.24.7",
    "ol": "9.2.4",
    "proj4": "2.11.0",
    "proj4leaflet": "1.0.2",
    "react": "18.3.1",
    "react-apexcharts": "1.4.1",
    "react-barcode": "^1.5.3",
    "react-calendar": "4.8.0",
    "react-chartjs-2": "5.2.0",
    "react-datepicker": "7.2.0",
    "react-daum-postcode": "^3.1.3",
    "react-dom": "18.3.1",
    "react-hook-form": "7.52.0",
    "react-icons": "5.2.1",
    "react-kakao-maps-sdk": "1.1.27",
    "react-leaflet": "4.2.1",
    "react-leaflet-cluster": "2.1.0",
    "react-leaflet-draw": "0.20.4",
    "react-leaflet-easyprint": "2.0.0",
    "react-loader-spinner": "6.1.6",
    "react-pdf": "7.7.3",
    "react-scroll": "1.9.0",
    "react-share": "5.1.0",
    "react-slick": "0.30.2",
    "react-to-print": "^2.15.1",
    "react-youtube": "10.1.0",
    "read-excel-file": "5.8.3",
    "recoil": "0.7.7",
    "sass": "^1.78.0",
    "slick-carousel": "1.8.1",
    "styled-components": "6.1.11",
    "swiper": "11.1.4",
    "url-parse": "1.5.10",
    "xlsx": "0.18.5",
    "zod": "3.23.8"
  },
// ...
}
```

---
### 2. `devDependencies`
###### ※ 정의
- 애플리케이션 **개발 또는 빌드 과정**에서만 필요한 패키지이다.
###### ※ 특징
- 테스트, 코드 빌드, 린트, 문법 체크 등 **개발 도구**를 포함한다.  
    (예: Webpack, ESLint, Jest 등)

- 배포 환경에서는 설치되지 않는다.  
    (`NODE_ENV=production` 설정 시 무시됨)
###### ※ 설치 방법
```bash
npm install <패키지> -save-dev
```
###### ※ 예시
- Webpack, ESLint와 같은 개발도구
```json
{
  "name": "project-name",
   // ...
  "devDependencies": {
    "@types/formidable": "2.0.5",
    "@types/jwt-decode": "^2.2.1",
    "@types/lodash": "^4.17.10",
    "@types/node": "20.16.1",
    "@types/react": "18.3.4",
    "@types/react-dom": "18",
    "@types/uuid": "^10.0.0",
    "eslint": "8",
    "eslint-config-next": "14.2.5",
    "typescript": "5.5.4"
  }
  // ...
}
```

---
### 3. 비교
| 특징         | dependencies          | devDependencies              |
|------------|-----------------------|------------------------------|
| 사용 시점      | 런타임(프로덕션 환경)          | 개발 및 빌드 단계                   |
| 사용 용도      | 애플리케이션 실행 시 필수        | 테스트, 빌드, 린트 등 개발 도구          |
| 예시 패키지     | React, Express, Axios | Webpack, Babel, Jest, ESLint |
| 설치 명령어     | npm install <패키지>     | npm install <패키지> --save-dev |
| 프로덕션 환경 설치 | 포함됨                   | 제외됨                          |

---
### 4. **실제 사용 예시**

###### 1. `dependencies`에 추가할 패키지
- **React 프로젝트**에서 사용자 인터페이스를 구성하기 위한 React 라이브러리
```bash
npm install react react-dom
```
###### 2. `devDependencies`에 추가할 패키지
- 코드 린트와 테스트를 위한 ESLint와 Jest 설치
```bash
npm install eslint jest --save-dev
```
###### 3. 배포 환경에서의 설치
- 배포 시에는 `devDependencies`를 제외한 `dependencies`만 설치하도록 설정해야 한다.
```bash
npm install --production
```


