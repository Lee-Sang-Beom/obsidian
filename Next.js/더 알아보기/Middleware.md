#### 1. Next.js Middleware

- `middleware`는 클라이언트 요청이 완료되기 전, 코드를 실행할 수 있도록 한다. 그런 다음, 들어오는 요청에 따라 응답을 재작성하게 하거나, `redirection`하거나, 요청 및 응답 헤더를 수정하거나, 직접 응답할 수도 있다.
	- `middleware`는 캐시된 콘텐츠와 라우트가 일치하기 전에 실행된다고 한다. 

- `middleware`의 정의는 프로젝트 루트에 `middleware.ts(또는 .js)`파일을 사용하여야 한다.


#### 2. Matching Paths

- `middleware`는 기본적으로 모든 라우트에 대해 아래와 같이 순차적으로 동작한다고 한다. 
	1. `next.config.js`에서의 **`header`** 동작
	2. `next.config.js`에서의 **`redirection`** 동작
	3. `middleware`에서의 동작 **`예시: rewrite, rediection`**
	4. `next.config.js`에서의 `beforeFiles` (재작성)
	5. 파일 시스템 `route`(라우트) `(public/, _next/static/, pages/, app/ 등)`
	6. `next.config.js`에서의 `afterFiles` (재작성)
	7. 동적 경로 (/blog/[slug])
	8. `next.config.js`에서의 `fallback` (재작성)

- 여기서 3번째 단계에서의 `middleware` 동작은 **파일 시스템 라우트, 동적 경로** 등의 동작보다 빨리 이루어진다.


#### 3. Matcher

- Matcher를 사용하면, 특정 경로에서 `middleware`를 필터링하여 실행할 수 있다.
```ts

export const config = {
	// matcher: '/about/:path' >> /about/a (o) | /about/a/b (x)
	matcher: '/about/:path*' // /about/a (o) | /about/a/b (o)
}

// 배열 구문을 사용하여 단일 경로 또는 여러 경로를 일치시킬 수 있다.
export const config = { matcher: ['/about/:path*', '/dashboard/:path*'], }

// 매처 구성은 전체 정규식을 허용하므로 부정적인 전방 탐색 또는 문자 일치와 같은 매칭이 지원된다.
// 특정 경로를 제외한 모든 것을 일치시키기 위한 예는 아래와 같다.
export const config = { matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)', ],}

```


#### 4. NextResponse

- Next.js에서는 웹 응답 API를 확장하여 추가적인 편의 기능을 제공하는 `NextResponse`를 제공한다.

- NextReponse API는 아래와 같은 작업을 수행할 수 있도록 한다.
	1. 들어오는 요청을 다른 URL로 `redirection`할 수 있다.
	2. 주어진 URL을 표시하여 응답을 재작성할 수 있다.
	3. API route, rewrite 대상에 대한 요청 헤더를 설정할 수 있다.
	4. 응답 쿠키를 설정할 수 있다.
	5. 응답 헤더를 설정할 수 있다.
	6. 물론, `next()`메소드를 사용하여, 그대로 진행할 수도 있다.
		- `next()`: 미들웨어에서 사용되며, 라우팅을 계속하고자 할 때 사용됩니다. 요청을 현재 상태로 반환한다. 
			- 또한, 응답을 생성할 때 헤더를 전달할 수 있다.

##### `redirect`

- Next.js의 `NextResponse`에 대한 여러 메소드
	- `NextResponse`의 모체는 [Web_api의 Response](https://developer.mozilla.org/ko/docs/Web/API/Response)이다.
```typescript
// `redirect()`: 지정된 URL로 리디렉션하는 응답을 생성한다.
// 요청된 url을 그대로 사용자에게 보여주지않고, '/'로 페이지를 이동시킴
return NextResponse.redirect(new URL('/', request.nextUrl.origin))
```

##### `rewrite`

-  `request.nextUrl.origin`에서 오는 요청에 대한 경로를 "`/me/categories`"로 `rewrite`
```typescript
// `rewrite()`: 원래 URL을 보존하면서 지정된 URL을 재작성(프록시)하는 응답을 생성한다.
// 요청된 url을 그대로 사용자에게 보여주지만, 컨텐츠는 /me/categories로 보여줌
return NextResponse.rewrite(new URL('/me/categories', request.nextUrl.origin))
```

##### `cookies`

-  Next.js는 `NextRequest`, `NextResponse`의 `cookies` 확장을 통해, `cookies`에 쉽게 접근하고 조작할 수 있는 편리한 방법을 제공한다.
	- 들어오는 요청`(request)`에 대해, `cookies`에는 다음과 같은 메서드가 있다.
		1. `get(name)` : 지정된 쿠키 이름에 해당하는 쿠키 값을 반환한다. 찾지 못할 경우 `undefined`를 반환하며, 여러 개의 쿠키가 있는 경우 첫 번째 것을 반환한다.
		2. `set(name, value)`: 지정된 이름과 값을 사용하여 응답에 쿠키를 설정한다.
		3. `getAll(name)`: 지정된 쿠키 이름에 해당하는 모든 쿠키 값을 반환한다. 이름이 주어지지 않으면 응답에 있는 모든 쿠키를 반환한다.
		4. `delete(name)`: 지정된 쿠키 이름에 해당하는 쿠키를 응답에서 삭제한다.
		5. `clear()`: 모든 쿠키를 제거한다. 

> **요청 및 응답헤더** 예시:  (출처: [Inpa Dev님의 포스트](https://inpa.tistory.com/entry/HTTP-%F0%9F%8C%90-%EC%9B%B9-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%9D%98-%EC%BF%A0%ED%82%A4-%EA%B0%9C%EB%85%90-Cookie-%ED%97%A4%EB%8D%94-%EB%8B%A4%EB%A3%A8%EA%B8%B0#cookie_%EC%9A%94%EC%B2%AD_%ED%97%A4%EB%8D%94))
![[Pasted image 20240325135110.png]]
![[Pasted image 20240325135200.png]]

##### `CORS`

- 공식문서에서는 CORS 헤더를 설정하여 간단한 요청 및 `preflighted` 요청을 포함한 교차 출처 요청을 허용할 수 있다고 한다.
```ts
import { NextRequest, NextResponse } from 'next/server'
 
const allowedOrigins = ['https://acme.com', 'https://my-app.org']
 
const corsOptions = {
  'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
  'Access-Control-Allow-Headers': 'Content-Type, Authorization',
}
 
export function middleware(request: NextRequest) {
  // 요청에서 origin 확인
  const origin = request.headers.get('origin') ?? ''
  const isAllowedOrigin = allowedOrigins.includes(origin)
 
  // `preflighted` 요청 처리
  const isPreflight = request.method === 'OPTIONS'
 
  if (isPreflight) {
    const preflightHeaders = {
      ...(isAllowedOrigin && { 'Access-Control-Allow-Origin': origin }),
      ...corsOptions,
    }
    return NextResponse.json({}, { headers: preflightHeaders })
  }
 
  // 간단한 요청 처리
  const response = NextResponse.next()
 
  if (isAllowedOrigin) {
    response.headers.set('Access-Control-Allow-Origin', origin)
  }
 
  Object.entries(corsOptions).forEach(([key, value]) => {
    response.headers.set(key, value)
  })
 
  return response
}
 
export const config = {
  matcher: '/api/:path*',
}
```

##### `Producing a Response`
- 아래는 `/api`로 시작하는 경로에 대해서만 `middleware` 실행 코드를 적용하도록 설정한 예제이다. (`matcher` 속성).
- 그리고 `middleware` 함수 내에서는 요청이 인증되었는지를 확인하기 위해 `isAuthenticated` 함수를 호출한다. 
	- 인증에 실패할 경우, 401 상태 코드와 함께 'authentication failed' 메시지를 가진 `JSON 응답`을 반환한다.
```ts
import { NextRequest } from 'next/server'
import { isAuthenticated } from '@lib/auth'

// match로 middleware내의 
export const config = {
  matcher: '/api/:function*',
}
 
export function middleware(request: NextRequest) {
  // Call our authentication function to check the request
  if (!isAuthenticated(request)) {
    // Respond with JSON indicating an error message
    return Response.json(
      { success: false, message: 'authentication failed' },
      { status: 401 }
    )
  }
}
```
#### 5. 예시

 - [예시 및 구현코드를 포함하는 포스트 출처](https://velog.io/@pds0309/nextjs-%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4%EB%9E%80)

-  요구기능
	1. 인증된 사용자만 접근할 수 있는 페이지에 토큰이 없는 사용자가 접근할 경우 메인페이지로 `redirect`시킨다.
	2. 로그인을 했던 사용자에게는 메인페이지 접근 시 본인 카테고리 목록 조회 페이지를 보여줘야한다.
	3. 그 외에는 요청에 대해 그대로 응답한다.

- 구현
 ```typescript
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  // 요청 객체에서 쿠키 뽑아내기
  const { cookies } = request;
  // accessToken 있는지 검사
  const hasCookie = cookies.has('accessToken');


  // 1. 쿠키도없고, 요청 URL이 home(/)이 아닌경우(인증된 사용자만 접근 가능한 페이지인경우)
  // 인증된 사용자만 접근할 수 있는 페이지에 토큰이 없는 사용자가 접근할 경우 메인페이지로 redirect시킨다
  if (!hasCookie && request.nextUrl.pathname !== '/') {
    return NextResponse.redirect(new URL('/', request.nextUrl.origin));
  }

  // 2. 쿠키있고, 요청 URL이 home(/)인 경우
  // 로그인을 한 사용자에게는 메인페이지 접근 시 본인 카테고리 목록 조회 페이지를 보여줘야한다.
  // 아래 예제에서는 rewrite로 URL은 요청한 경로 그대로를 보여주는데, 실 내용은 '/me/categories'로 보여준다
  if (hasCookie && request.nextUrl.pathname === '/') {
    return NextResponse.rewrite(new URL('/me/categories', request.nextUrl.origin));
  }
  
  // 3. 그 외에는 요청에 대해 그대로 응답한다.
  return NextResponse.next();
}

export const config = {
  matcher: ['/me/:path*', '/write/:path*', '/'],
};
```

#### 기타
- **nextauth 백링크**: [[3. NextAuth.js 사용해보기 3]]
