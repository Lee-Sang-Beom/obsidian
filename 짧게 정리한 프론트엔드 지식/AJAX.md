
#### 1. AJAX(Asynchronous JavaScript and XML)란?

- 웹 페이지가 새로고침 없이 서버와 데이터를 주고받을 수 있도록 하는 기술이다.
	- 해당 기술을 통해 사용자 경험을 개선하고, 페이지 전체를 다시 로드하지 않고도 서버와 상호작용하는 애플리케이션을 만들 수 있다.
	- AJAX는 주로 `XMLHttpRequest` 객체나 최신 브라우저 환경에서는 `fetch` API를 사용해 구현된다.
	- 일반적으로 서버에서 받아오는 데이터 형식은 XML뿐만 아니라 JSON, HTML, 텍스트 등도 가능하다.

- 일반적으로 HTTP의 통신은 아래와 같은 특징을 가진다.
    - **단방향 통신** : 사용자가 서버에게 요청을 보내는 단방향 통신이다. 
	- **connectionless(비연결성)** : 클라이언트가 요청을 한 후 서버로부터 응답을 받으면, TCP/IP가 그 연결을 끊어버린다.
	-  **stateless(무상태)** : 통신이 끝나면 상태를 유지하지 않는다.

- 과거, AJAX가 없던 MPA 방식에서는 위와 같은 HTTP 통신을 매 요청마다 수행하고, 그 응답으로 받은 리소스로 페이지 전체를 갱신했어야 했었다.
    - 그렇기에, 자원낭비와 시간낭비가 심했다.

 
#### 2. AJAX의 주요 특징

1. **비동기 처리
	- 서버 요청이 진행되는 동안에도 페이지의 다른 작업이 가능하여, 성능과 사용자 경험을 향상시킨다.
	- AJAX는 비동기 방식을 사용하기 때문에, 처리가 완료될때까지 프로그램이 멈추는(기다리는) 상태를 방지할 수 있습다. 이를 통해 자원과 시간을 아낄 수 있다.
	    - 하지만, 연속으로 데이터를 요청하면 서버부하가 증가할 수 있다.

2. **부분 업데이트**
	- 전체 페이지를 다시 로드하지 않고, 필요한 데이터만 받아와서 특정 부분만 업데이트할 수 있습니다.
	- Ajax는 화면을 페이지 새로고침없이 갱신할 수 있도록 함으로써, 웹페이지 속도를 향상시킨다.
    
3. **다양한 데이터 형식**
	- XML뿐만 아니라 JSON, HTML, 텍스트와 같은 다양한 형식으로 데이터를 주고받을 수 있다.


#### 3. `XMLHttpRequest` 사용 예시 (기록은 일단 해 놓았지만 잘 모르겠음)

```js
const xhr = new XMLHttpRequest();
xhr.open('GET', 'https://api.example.com/data', true);
xhr.onreadystatechange = function () {
  if (xhr.readyState === 4 && xhr.status === 200) {
    // 서버로부터 받은 응답 처리
    console.log(JSON.parse(xhr.responseText));
  }
};
xhr.send();
```

- 위 코드에 대한 대략적인 흐름은 아래와 같다.
	- `open` 메서드로 요청 설정(`GET`, `POST` 등)
	- `onreadystatechange` 콜백으로 서버 응답 처리
	- `send` 메서드로 요청 전송

- 이를 통해, AJAX의 통신 단계는 크게 2단계 정도로 나눌 수 있음을 알 수 있다.
    - 1. 요청 시, 브라우저는 XHR 객체를 만들어 서버에 정보를 요청한다.
    - 2. 요청의 결과로, 서버가 응답을 보내주면, 브라우저는 받아온 리소스를 처리하여 페이지에 추가한다.


#### 4. `fetch` API 사용 예시

- `fetch` API는 `XMLHttpRequest`보다 더 간단하고 직관적인 방식으로 서버 요청을 처리할 수 있다.
	- `Promise`를 반환하기 때문에, 비동기 처리를 위한 `async/await`나 `then`을 쉽게 사용할 수 있다.

```js
fetch('https://api.example.com/data')
  .then(response => response.json())
  .then(data => {
    // 서버로부터 받은 응답 처리
    console.log(data);
  })
  .catch(error => console.error('Error:', error));
```

- 또, `async/await`을 사용할 수 있다.
```js
async function fetchData() {
  try {
    const response = await fetch('https://api.example.com/data');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error('Error:', error);
  }
}
fetchData();
```


#### 5. 장점 및 단점

>장점
- **더 빠른 사용자 경험**: 페이지를 전체 새로고침하지 않으므로 응답 속도가 빠르고, 사용자 경험을 크게 향상시킨다.
- **서버 부하 감소**: 필요한 데이터만 요청하고 업데이트할 수 있으므로 불필요한 데이터 전송을 줄일 수 있다.


>단점
- **JavaScript 의존성**: JavaScript가 비활성화된 브라우저에서는 정상적으로 동작하지 않을 수 있다.
- **CORS 제한**: 다른 도메인에 요청할 때는 CORS(Cross-Origin Resource Sharing) 정책으로 인해 제한이 있을 수 있다.
	- 서버에서 이를 허용하도록 설정해야 한다.

