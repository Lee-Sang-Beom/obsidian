- `require`와 `import`는 각각 JavaScript의 모듈을 불러오는 방법이지만, 두 방식은 문법과 사용되는 환경에서 차이가 있다.

#### 1. Require

- **사용환경** : 주로 Node.js 환경에서 사용된다.
- **모듈 시스템** : Commonjs 모듈 시스템을 따른다.
- **동적 로딩 가능** : 조건에 따라 모듈을 불러오는 등의 동적 사용이 가능하다.
- **호출방법** : `require(모듈 경로)` 방식으로 호출한다.
	- `require`를 사용하여 모듈을 불러올 때는, 마치 변수에 값을 할당하듯 진행한다는 특징이 있다.
```js
// require 예시
const fs = require('fs'); // Node.js 파일 시스템 모듈을 불러옴

fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});
```

- **단일 객체 내보내기** : 하나의 객체만 내보내면 `module.exports` 키워드를 사용한다.
> `logger.js`
```js
const logger = {
    log: (message) => console.log(message),
    error: (message) => console.error(message)
};

module.exports = logger;
```

>`app.js`
```js
const logger = require('./logger');

logger.log('This is a log message'); // This is a log message
logger.error('This is an error message'); // This is an error message
```


- **어러 객체 내보내기** : 여러 객체를 내보낼 때는 `exports.variable` 형태를 사용한다.
> `mathUtils.js`
```js
const add = (a, b) => a + b;
const subtract = (a, b) => a - b;

module.exports = {
    add,
    subtract
};
```

>`app.js`
```js
const mathUtils = require('./mathUtils');

console.log(mathUtils.add(2, 3)); // 5
console.log(mathUtils.subtract(5, 2)); // 3
```


#### 2. Import

 - **사용환경**: 주로 브라우저 환경과 최신 JavaScript 문법(ES6 모듈)에서 사용되며, Node.js에서도 ES 모듈 지원 설정으로 사용할 수 있다. 
	 - `import`는 ES6에서 도입되었으며, Babel과 같은 ES6코드를 변환해주는 도구를 필요로 한다.
 
 - **모듈 시스템** : **ES6 모듈 시스템**을 따른다.
 - **정적 로딩**: 파일 로딩이 코드 상단에서 정적으로 결정된다.
 - **구문**: `import` 키워드로 모듈을 불러온다.

- **단일 객체 내보내기** : 하나의 객체만 내보내면 `export default` 키워드를 사용한다.
> `logger.js`
```js
const logger = {
    log: (message) => console.log(message),
    error: (message) => console.error(message)
};

export default logger;
```

> `app.js`
```js
import logger from './logger.js';

logger.log('This is a log message'); // This is a log message
logger.error('This is an error message'); // This is an error message
```


- **여러 객체 내보내기** : 여러 객체를 내보낼 때는 `export const` 키워드를 사용한다.
> `mathUtils.js`
```js
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;

```

> `app.js`
```js
import { add, subtract } from './mathUtils.js'; // 중괄호로 불러올 객체 선택지정 가능

console.log(add(2, 3)); // 5
console.log(subtract(5, 2)); // 3

```


#### 3. 주요 차이점

| 특성      | require                   | import                   |
|---------|---------------------------|--------------------------|
| 모듈 시스템  | CommonJS                  | ES6 모듈                   |
| 주 사용 환경 | Node.js                   | 브라우저, Node.js (최신)       |
| 로딩 방식   | 동적 로딩 가능                  | 정적 로딩                    |
| 사용 구문   | const fs = require('fs'); | import fs from 'fs';     |
| 파일 확장자  | .js, .json 등              | .js (Node.js에서는 .mjs 필요) |
- 일반적으로 `import`가 사용자가 필요한 모듈 부분만을 선택해 로드할 수 있다는 점에서 성능이 우수하고 메모리를 절약할 수 있다.
