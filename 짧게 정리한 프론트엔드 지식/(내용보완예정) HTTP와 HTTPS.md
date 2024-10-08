
## HTTP

- 서버/클라이언트 모델에서, 데이터를 주고받기 위한 프로토콜입니다.

---

## HTTPS

- HTTP에 SSL이라는 암호화 기반 인터넷 보안 프로토콜을 추가한 것입니다. 

---

### 차이

1. HTTP는 암호화가 추가되지 않았기 떄문에 보안에 취약합니다. HTTPS는 암호화된 연결로 안전하게 데이터를 주고받을 수 있습니다. 
    - 또 구글에서는 HTTP 반영여부에 따라 검색순위 결과에 가산점을 주겠다고 언급한 적이 있습니다.

3. 지금에 와서는 큰 차이가 없지만, HTTPS는 암호화/복호화과정을 필요로하므로 연산속도가 HTTP에 비해 상대적으로 느립니다.

4. HTTPS는 인증서를 발급하고 유지하기 위한 추가비용이 발생합니다.

---

### HTTPS: 대칭키와 비대칭키 암호화 방식 사용

- HTTPS에서는 대칭키 암호화방식과 비대칭키 암호화 방식을 함께 사용합니다. 
    - 세션키는 주고 받는 데이터를 암호화하기 위해 사용합니다. 이것은 대칭키입니다. (***연산속도 빠름***)
    - 이 세션키 자체를 서버와 클라이언트가 안전하게 교환하는 과정에서는 비대칭키가 사용됩니다.

---

### (추가 : 세션 키 공유)

- 개인키로 암호화한 것은 공개키로, 공개키로 암호화한 것은 개인키로 복호화할 수 있음을 기억해야 합니다.

1. 클라이언트(브라우저)가 서버로 최초 연결 시도를 합니다.

2. 서버는 인증서를 브라우저에게 넘겨줍니다.
    - 서버는 사전에 비대칭키 발급을 받기위해, CA기업에게 돈을 지불하고 공개키를 저장한 인증서 발급을 요청합니다.
    - CA기업은 서버의 공개키를 포함한 인증서를 만들어, CA기업의 개인키로 암호화해 서버에게 응답합니다.
    - 그리고 서버는 클라이언트에게 이 인증서를 제공합니다.

3. 브라우저는 인증서의 유효성을 검사하고 세션키를 발급합니다. 
    - 브라우저는 CA기업의 공개키로 인증서를 복호화합니다.

4. 브라우저는 복호화된 인증서로 이제 세션키를 공유합니다. 
    - 브라우저 측에 세션키를 저장합니다.
    - 그리고, 인증서에서 꺼낸 서버의 공개키로 세션키를 암호화하여 서버로 전송합니다.

5. 서버는 개인키를 이용해서, 전달받은 세션키를 복호화합니다.

6. 이 과정으로 세션키를 서버와 클라이언트가 둘 다 갖고 있습니다. 

