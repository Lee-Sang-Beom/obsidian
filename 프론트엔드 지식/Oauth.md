> 참고문서 및 내용출처 1: [삽질로그님의 포스트](https://velog.io/@goldbear2022/NextAuth%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%B4%EC%84%9C-%EA%B5%AC%EA%B8%80-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%97%B0%EB%8F%99-%EA%B0%80%EC%9E%85-%EA%B8%B0%EB%8A%A5-%EB%A7%8C%EB%93%A4%EA%B8%B0-%E4%B8%8A#:~:text=OAuth%EB%8A%94%20%EC%9D%B8%ED%84%B0%EB%84%B7%20%EC%82%AC%EC%9A%A9%EC%9E%90%EB%93%A4%EC%9D%B4,%ED%95%B4%EC%A3%BC%EB%8A%94%20%EC%98%A4%ED%94%88%20%EC%8A%A4%ED%83%A0%EB%8B%A4%EB%93%9C%20%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C%EC%9D%B4%EB%8B%A4.)

> 참고문서 및 내용출처2 : [Furo님의 포스트](https://iam.furo.one/post/concept-oauth)

#### 1. OAuth(Open Authorization)

- 애플리케이션을 이용할 때, **구글, 카카오** 등의 서비스에 저장된 계정 정보를 이용하여, 로그인 해 본 적이 있을 것이다.
	- 이러한 기능은 **OAuth**라는 인증 표준에 의해 구현된다.

- OAuth를 이용하면, **서드파티 앱** (제조사나 통신사에서 만든 기본 탑재가 아닌 일반 앱 스토어 등에서 다운받을 수 있는 앱)이 사용자의 개인정보를 직접 접근할 필요 없이 원하는 기능을 동작하게끔 할 수 있다.
	- 또한, 사용자가 필요로하는 데이터를 안전하게 공유할 수 있다.

- OAuth는 사용자 인증을 위한 기술 표준으로, 사용자명이나 비밀번호 등의 **실제 사용자 자격 증명을 공유**하지 않고, 한 서비스에서 다른 서비스로 권한 부여를 전달하기 위한 프로토콜이다.
	- 여기서 중요한 것은 **특정 서비스에서 보호되고 있는 리소스에 접근하기 위해, 사용자 인증(Authentication)을 통해 사용자의 리소스 접근 권한(Authorization)을 위임받는 것**이다.

| 구분                     | 내용                                                           |
| ---------------------- | ------------------------------------------------------------ |
| Authentication<br>(인증) | 해당 사용자가 자신이 보호된 리소스에 접근할 수 있는 사용자인지 확인하는 것으로, **식별**과 연관된 개념 |
| Authorization<br>(인가)  | 해당 사용자에게 리소스에 접근할 수 있는 권한을 부여하는 것으로, **접근**과 연관된 개념          |


#### 2. 특징

- OAuth는 **다른 애플리케이션에 있는 서버와 통신하는 방식으로 권한 부여를 전달**하기 때문에, OAuth에 대해 더 알아보기 위해서는, 특정 개념에 대해 이해하고 있어야 한다.

> **Server** (Authorization Server + Resource Server)
- 사용자의 허가를 검증하고, 클라이언트에 응용 애플리케이션에 보유한 사용자의 리소스를 공유하는 서버이다.

- 일반적으로 허가를 담당하는 검증 서버(Authorization Server)와 실제 사용자 리소스를 보유한 리소스 서버(Resource Server, or API Server)로 분리되어 있다.
	- 예시로 **구글 서버**를 들 수 있는데, 이 구글 서버는 리소스를 사용할 애플리케이션이 안전한지에 대한 인증과정을 정상적으로 거친 경우에 한정하여, 애플리케이션이 필요로 하는 리소스를 응답한다.  
	- 이 때, 애플리케이션이 안전한 지에 대하여 인증을 거치는 작업과정을 **등록(Register)** 이라고 한다. 
	- 등록 과정을 거치려면, 구글 클라우드에 접속하고 클라이언트 ID와 비밀번호를 발급받아야 한다.

> **Resource Owner / User**
- 말 그대로, 리소스를 소유한 사용자를 의미한다.
- 애플리케이션에서 리소스를 사용할 수 있도록, 리소스의 공유 요청을 허용할 것인지를 결정하는, OAuth의 최종 권리자이다.

> **Client(or Consumer)**
- 사용자의 허가를 받아, 사용자의 리소스에 접근할 수 있도록 요청하는 응용 애플리케이션
 
> **Access Token**
- Access Token(액세스 토큰)은 애플리케이션이 사용자(Resource Owner)에게 권한을 위임받은 이후 **리소스를 얻기위해 사용되는 인증 수단**이다.
- Access Token(액세스 토큰)은 인증 서버(Authorization Server)에서 발급해주며, 이를 받기 위해서는 애플리케이션에서 요청을 보내주어야 한다.
	- 이 때, **등록 과정에서 만들었던 클라이언트 ID와 비밀번호를 함께 보내야 한다.**


#### 3. 동작

![[oauth 인증과정.png]]
- 동작 과정은 위 이미지와 같으며, 그에 따른 설명은 아래와 같다.
	1. **Resource Owner(User)** 가 Client 서비스를 이용하고자, Client에게 서비스 이용 요청읇 보낸다.
	2. **Client**는 **Authorization(Auth) Server** 측에 **Access Token**을 요청한다.
	3. Authorization Server는 Resource Owner에게 Client 서비스 이용을 위한 인가 동의를 요청한다.
	4. Resource Owner는 Authorization Server에게 인가 동의에 대한 응답을 진행한다
	5. Authorization Server는 Resource Owner에 대한 인가 동의 응답을 받으면, **Access Token을 생성해, Client에게 전달**한다.
	6. Client는 Access Token을 저장한다.
	7. Client는 Access Token을 가지고, **Resource Server**에 요청을 보낸다.
	8. Resource Server는 Access Token의 유효성을 검사한 다음, 요청한 리소스를 Client에게 응답한다.
	9. Client는 Resource Owner가 맨 처음 보냈던 서비스 이용 요청에 대한 응답을 전달한다.


#### 4. 갱신

![[oauth refreshtoken.png]]
- Access Token은 갱신 주기가 짧기 때문에, 만료 시마다 서버에 재요청을 진행해주어야 한다.
	- 많은 **Resource Owner**가 존재한다고 가정하고, Client가 만료된 Access Token을 갱신하기 위해, Authorization Server에게 Access Tooken을 재발급받으려 하는 상황을 생각해보자. 
		- 통신 횟수가 굉장히 많이 늘어나기 때문에 이러한 상황은 비효율적이라 볼 수 있다.
	- 이런 문제를 해결하기 위해, Authorization Server에서는 Access Token 외에도 추가적으로, **Reffresh Token**이라는 것도 추가로 발급해준다.
	- Refresh Token이 유효하면, Access Token을 재발급해준다.
