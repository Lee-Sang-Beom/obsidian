
#### 1. SOP(Same Origin Policy)

- **SOP**(Same Origin Policy): 다른 출처(Origin)의 리소스를 사용하는 것을 제한하는 보안 정책
	- 즉, 같은 출처에서만 리소스를 공유할 수 있다는 의미를 가진 정책이다.

- 브라우저는 기본적으로 **동일 출처 정책(SOP)** 을 지켜, 다른 **출처(Origin)** 의 리소스 접근을 금지한다.
	- 개발환경이나 Postman을 사용하여 API를 테스트할 때는 문제가 없다가, 브라우저 상에서 API를 호출할 때 문제가 발생하는 이유가 브라우저가 SOP를 준수하기 때문인 것이다.

- 출처(Origin)를 비교하는 로직은 서버에서 구현된 스펙이 아닌 **브라우저에 구현된 스펙이다.**  
	- 그래서 CORS 정책을 위반하는 요청에 대해 서버가 정상적으로 응답을 하더라도, 브라우저가 이 응답을 분석하여 특정 요청이 CORS 정책에 위반된 것임을 확인하게 되면 그 응답은 정상적으로 처리하지 않게 된다.


#### 2. CORS(Cross-Origin Resource Sharing)

> [이미지 출처: '김대은'님의 Velog 포스트 ](https://etloveguitar.tistory.com/83)
![[cors err.png]]

- **CORS**(Cross-Origin Resource Sharing): 추가적인 HTTP Header를 사용하여, 한 출처에서 실행중인 웹 애플리케이션이 다른 출처에 있는 외부 리소스에 **접근할 수 있는 권한을 부여할 수 있도록 브라우저에게 알려주는 정책**

- CORS Error는 아래와 같은 **권고사항**이라고 생각하면 된다.
	1. 브라우저가 현재 SOP 정책을 준수하여 다른 출처의 리소스를 사용하지 못하게 막아놓았다.
	2. 다른 출처의 리소스를 정상적으로 응답받고 싶다면, CORS를 허용하여 아무런 문제없이 다른 출처의 리소스 공유를 할 수 있도록 하라.


#### 3. Origin(출처)

> [이미지 출처: '김대은'님의 Velog 포스트 ](https://etloveguitar.tistory.com/83)

![[URL.png]]
- Origin(출처)는 위 이미지와 같은 URL 구조에서 **Protocol, Host, Port**를 합친 것을 의미한다.
	- **Protocol, Host, Port**가 모두 일치하면, Same Origin이다.
	- **Protocol, Host, Port** 중 하나라도 일치하지 않는다면 Cross Origin이다.

- 정리하면, Cross-Origin은 다음 중 하나라도 다른 경우에 발생한다.
	- 프로토콜 (`http !== https`)
	- 도메인 (`domain.com !== other-domain.com`)
	- 포트번호 (`:3000port !== :8000port`)


#### 4. CORS 동작 과정

##### > STEP 1. 클라이언트에서, HTTP request header에 Origin을 담아 전달한다.

> [이미지 출처: '김대은'님의 Velog 포스트 ](https://etloveguitar.tistory.com/83)
![[requestHeaderOrigin.png]]
- 먼저, 웹 애플리케이션이 다른 Origin(출처)의 리소스에 접근하려고 할 때, HTTP 프로토콜을 사용해 요청을 보낸다.
	- 이 때, request header에는 **Origin** 필드에 요청을 보내는 출처를 포함시킨 상태로 보낸다.

##### > STEP 2. 서버는 HTTP response header에 Access-Control-Allow-Origin을 담아 클라이언트에게 전달한다.

> [이미지 출처: '김대은'님의 Velog 포스트 ](https://etloveguitar.tistory.com/83)
![[responseHeaderOrigin.png]]
- 이후, 서버는 HTTP response header 내에  **Access-control-allow-origin** 필드에 리소스 접근을 허용할 출처(Origin)를 포함하여 응답한다.

##### > STEP 3. 클라이언트에서, 자신이 보냈던 요청의 Origin과 서버가 보내준 Access-Control-Allow-Origin을 비교한다.

- 그리고 브라우저는 request header의 Origin 필드값과 response header의 Access-control-allow-origin 필드값을 비교한다.

- 조금 더 정확히 말하자면, 브라우저는 response header의 Access-control-allow-origin 필드값 목록에 요청을 보낸 측의 출처(Origin)가 포함되어있는지를 검사하는 것이다.

> [!note] 주의) 서버의 응답은 CORS 위반 여부와 관계없다.
> - 위에서 여럿 말했듯, CORS 정책에 의하여, Origin을 비교하는 작업은 서버 측의 스펙이 아닌, 브라우저 측의 스펙이다.
> - 따라서, 서버에서 정상적으로 200 status code를 보내도, 브라우저에서 이 응답이 CORS 정책을 위반했다고 분석할 수 있는 것이다.
> 	- 서버 측 로그에서는 정상 응답했다는 로그만 남으므로, CORS를 정확히 이해해야 이 Error를 해결할 수 있다.


#### 5. CORS 실제 작동 시나리오  (단순 요청 : Simple Request)

- CORS가 실질적으로 작동하는 유형은 총 3가지가 있는데, 여기서는 먼저 simple request에 대해 알아보자

> [이미지 출처: '김대은'님의 Velog 포스트 ](https://etloveguitar.tistory.com/83)
![[simple request.png]]


- **Simple Request**방식은 **Preflight Request**(예비 요청) 과정 없이 서버에 본 요청을 보내는 방법이다.
	- 그리고, 서버가 reponse header에  **Access-control-allow-origin** 필드를 포함해 응답하면, 브라우저는 요청 시 request header에 포함했던 **Origin** 필드값과의 비교를 통해 CORS 정책 위반을 하였는지를 검사한다.

- 이 때의 요청 메소드는 **GET, HEAD, POST** 중 하나여야만 한다.

- 또한, user-agent가 자동으로 설정한 헤더 필드 외에, 수동으로 설정 가능한 헤더 필드는 **Fetch 명세에서 `CORS-safelisted request-header`로 정의된 것들만 사용할 수 있다.**
	- `Accept(클라이언트가 지원하는 미디어 타입)`
	- `Accept-Language(클라이언트가 선호하는 언어)`
	- `Content-Language(해당 리소스의 언어)
	- `Content-Type(클라이언트 측 본문 데이터 유형)`
	- `DPR(디바이스 픽셀비율)`
	- `Downlink(사용자 네트워크 다운링크 속도)`
	- `Save-Data(사용자가 데이터를 절약하는 모드를 사용 중인가?)`
	- `Width(뷰포트 너비)`
	- `Viewport-Width(뷰포트 가로너비)`

- `Content-Type(클라이언트가 서버에게 전송하는 본문 데이터의 유형)`을 사용하는 경우에는 다음의 값들만 허용된다.
	- `application/x-www-form-urlencoded(HTML 폼데이터), multipart/form-data(파일 업로드와 같은 다중부분 폼 데이터), text/plain(텍스트데이터)`
	- `application/json, application/xml`은 사용할 수 없다.
		- 보통 POST method는 `apllication/json`을 많이 사용하기 때문에 그닥 잘 사용하지 않는 방식이다.

- 헤더에 대하여 더 궁금하다면, [여기](https://velog.io/@leemember/HTTP-%ED%97%A4%EB%8D%94)를 참고하자.


#### 6. CORS 실제 작동 시나리오 (예비 요청 : Preflight Request()

- 해당 유형은 브라우저가 요청을 한번에 보내지 않고, **예비 요청과 본 요청을 나눠 전송**하는 방법이다.
	- 대부분의 CORS 실제 작동은 해당 방식으로 이루어진다고 생각하면 된다. 
    
- 이 때, 본 요청보다 앞서 보내는 예비 요청을 **Preflight**이라 부른다.
	- 예비 요청의 역할은 본 요청을 보내기 전에, 브라우저 스스로 안전한 요청인지를 확인하는 데에 있다.
	
- 예비 요청은, 실제 요청에 사용할 메소드와 헤더 등을 포함하여 요청을 보낸다.
	- 예비 요청의 메소드는 **GET, POST**가 아닌,  **OPTION** 메소드가 사용된다.

> [이미지 출처: '김대은'님의 Velog 포스트 ](https://etloveguitar.tistory.com/83)
![[preflight request.png]]

- 대체적인 순서는 위의 이미지와 같다고 생각하면 된다. 예시로 순서를 들어보자.
	1. 제일 먼저, 자바스크립트 코드에서 `Fetch() API`를 통해 서버로부터 리소스를 받아오고자 한다.
	2. 브라우저는 해당 `Fetch() API` 에 대한 본 요청에 앞서, **예비 요청**을 서버로 먼저 보낸다.
	3. 서버가 예비 요청에 대한 응답으로  `Access-Control-Allow-Origin`, 메소드, 헤더 등등의 정보를 response header에 담아 반환한다.
    
	- 응답을 받으면, 브라우저는 **응답 정보와 자신이 전송한 실제 요청에 사용할 정보(메소드와 헤더)를 비교**해 실제 요청의 안정성을 검사합니다.
    
	- 안정성 검사가 완료되면, 브라우저측은 본 요청을 보냅니다.
    
	- 본 요청에 대한 응답을 받으면 브라우저는 최종적으로 응답 데이터를 자바스크립트에 넘겨줍니다.
    

---

### 인증 정보를 포함한 요청 (CORS요청의 유형 3)


- 인증 정보를 포함한 요청의 경우, **클라이언트 측**에서는 **withCredentials(크리덴셜)**과 같은 별도의 설정을 해 주어야 합니다. 별도 설정을 해주지 않으면, **인증정보는 자동으로 서버에 전송**되지 않습니다.
    
- 응답 시에는 **Access-control-allow-origin 헤더에는 와일드카드가 아닌 분명한 출처를 설정**해주어야 하고, **Access-Control-Allow-Credentials를 true**로 설정해주어야 합니다. 그렇지 않으면 **브라우저에 의해 응답이 거부**됩니다.








#### 6. CORS 에러 해결 방법


- CORS 에러를 해결하고, 외부 리소스를 위해서는 서버의 응답에 특정 헤더를 포함하는 방식으로 해결할 수 있습니다.
- 서버 측에서 **Access-Control-Allow-Origin** 헤더에 명시적으로 특정 출처값를 입력해주면, 입력한 해당 출처의 페이지에서는, 외부 리소스 접근이 가능하게 됩니다


##### **그 외)**


1. Access-Control-Allow-Method: 특정 HTTP Method만 리소스에 접근이 가능하도록 허용합니다.
2. Access-Control-Expose-Headers: 자바스크립트에서 헤더에 접근할 수 있도록 허용합니다.

---


