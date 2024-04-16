> [참고자료 : 김츄의 개발일지](https://velog.io/@chuu1019/Access-Token%EA%B3%BC-Refresh-Token%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%EC%99%9C-%ED%95%84%EC%9A%94%ED%95%A0%EA%B9%8C)

- Access Token과 Refresh Token은 사용자 인증 및 세션 관리에 사용되는 보안 토큰이다.
	- 해당 토큰들은 웹 애플리케이션 및 API 서버와 사용자 간의 인증 및 권한 부여를 위해 중요한 역할을 한다.

1. **엑세스 토큰 (Access Token)**
    - 엑세스 토큰 (Access Token)은 사용자를 인증하고 해당 사용자에 대한 권한을 부여하는 데 사용된다.
    - 주로 웹 애플리케이션에서 로그인한 사용자에 대한 세션을 관리하거나 API 요청을 인증하는 데 사용된다.
    - 보통 JWT (JSON Web Token) 형식으로 표현되며, 사용자 ID, 권한, 만료 시간 등의 정보를 포함할 수 있다.
    - 엑세스 토큰 (Access Token)은 유효 기간이 짧고, 일반적으로 한 번 사용하면 만료되는 특성을 가진다.

2. **리프레시 토큰 (Refresh Token)**
    - 리프레시 토큰(Refresh Token)은 주로 **엑세스 토큰(Access Token)** 을 새로 고쳐야 할 때 사용된다. 엑세스 토큰 (Access Token)의 유효 기간이 만료되었을 때, 리프레시 토큰(Refresh Token)을 사용하여 새로운 엑세스 토큰 (Access Token)을 얻을 수 있다.
    - 리프레시 토큰(Refresh Token)은 엑세스 토큰 (Access Token)의 재발급을 위해 사용되며, 일반적으로 엑세스 토큰 (Access Token)보다 유효 기간이 길기 때문에 보안에 민감한 정보를 담고 있을 수 있다.
    - 리프레시 토큰(Refresh Token)은 일반적으로 사용자가 로그인 상태를 유지하거나 장기간 사용자를 식별하기 위해 사용된다.

- 즉, 통신과정에서 탈취당할 위험이 큰 Access Token의 만료 기간을 짧게 두고 **Refresh Token(Refresh Token)으로 주기적으로 재발급함으로써 피해을 최소화**한 것이다.

- 일반적인 인증 흐름에서, 사용자가 인증되면 엑세스 토큰 (Access Token)을 받게 되고, 이 엑세스 토큰 (Access Token)은 주로 세션 유지 및 API 요청을 보내는 데 사용된다.
	- 그러나 엑세스 토큰 (Access Token)의 유효 기간이 만료되면 리프레시 토큰을 사용하여 새로운 엑세스 토큰 (Access Token)을 얻게 된다. 이렇게 함으로써 사용자는 지속적으로 로그인 상태를 유지할 수 있다.
	- 보안을 위해 이러한 토큰들은 안전하게 관리되어야 하며, HTTPS와 같은 보안 프로토콜을 통해 전달되어야 한다. 
```tsx

// 사용 예시
async function getHeaders(req: NextRequest) {
  // nextauth
  const session = await getSession();
  return {
    "X-AUTH-TOKEN": session?.user.jwtAuthToken,
    Cookie: "X-REFRESH_TOKEN=" + session?.user.jwtRefreshToken,
    withCredentials: true,
  };
}

```

- 추가적으로, Refresh Token은 통신 빈도가 적은 편이긴 하지만, 탈취에 따른 보안 위험이 아예 없지는 않다.
	- **OAuth**에서는, Refresh Token Rotation 방법을 제시하고 있다.
	- 이는 클라이언트가 Access Token를 재요청할 때마다 Refresh Token도 새로 발급받는 방법이다.
		- 이렇게 되면 탈취자가 가지고 있는 Refresh Token은 더이상 만료 기간이 긴 토큰이 아니게 된다. 
		- 따라서 불법적인 사용의 위험은 줄어든다.

