
1. `request.nextUrl.origin`에서 오는 요청에 대한 경로를 "`/`"로 `redirect`
```typescript
// 요청된 url을 그대로 사용자에게 보여주지않고, '/'로 페이지를 이동시킴
return NextResponse.redirect(new URL('/', request.nextUrl.origin))
```

2. `request.nextUrl.origin`에서 오는 요청에 대한 경로를 "`/me/categories`"로 `rewrite`
```typescript
// 요청된 url을 그대로 사용자에게 보여주지만, 컨텐츠는 /me/categories로 보여줌
return NextResponse.rewrite(new URL('/me/categories', request.nextUrl.origin))
```

3. Next.js의 NextResponse에 대한 여러 메소드
	- NextResponse의 모체는 [이거](https://developer.mozilla.org/ko/docs/Web/API/Response)임 (Web_api의 Response)
```
  
Next.js에서는 웹 응답 API를 확장하여 추가적인 편의 기능을 제공하는 `NextResponse`를 제공합니다.

- `cookies`: 응답의 Set-Cookie 헤더를 읽거나 변경할 수 있습니다.
    
    - `set(name, value)`: 지정된 이름과 값을 사용하여 응답에 쿠키를 설정합니다.
    - `get(name)`: 지정된 쿠키 이름에 해당하는 쿠키 값을 반환합니다. 찾지 못할 경우 `undefined`를 반환하며, 여러 개의 쿠키가 있는 경우 첫 번째 것을 반환합니다.
    - `getAll(name)`: 지정된 쿠키 이름에 해당하는 모든 쿠키 값을 반환합니다. 이름이 주어지지 않으면 응답에 있는 모든 쿠키를 반환합니다.
    - `delete(name)`: 지정된 쿠키 이름에 해당하는 쿠키를 응답에서 삭제합니다.
- `json()`: 주어진 JSON 바디로 응답을 생성합니다.
    
- `redirect()`: 지정된 URL로 리디렉션하는 응답을 생성합니다.
    
- `rewrite()`: 원래 URL을 보존하면서 지정된 URL을 재작성(프록시)하는 응답을 생성합니다.
    
- `next()`: 미들웨어에서 사용되며, 라우팅을 계속하고자 할 때 사용됩니다. 요청을 현재 상태로 반환합니다.
    

또한, 응답을 생성할 때 헤더를 전달할 수 있습니다.
```

4. 요구기능
```null
- 1. 로그인을 했던 사용자에게는 메인페이지 접근 시 본인 카테고리 목록 조회 페이지를 보여줘야한다.
- 2. 인증된 사용자만 접근할 수 있는 페이지에 토큰이 없는 사용자가 접근할 경우 메인페이지로 redirect시킨다.
- 3. 그 외에는 요청에 대해 그대로 응답한다.
```

5. 구현
 ```typescript
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  // 요청 객체에서 쿠키 뽑아내
  const { cookies } = request;
  // accessToken 있는지 검사
  const hasCookie = cookies.has('accessToken');
  if (!hasCookie && request.nextUrl.pathname !== '/') {
  // 2. 인증된 사용자만 접근할 수 있는 페이지에 토큰이 없는 사용자가 접근할 경우 메인페이지로 redirect시킨다.
    return NextResponse.redirect(new URL('/', request.nextUrl.origin));
  }
  if (hasCookie && request.nextUrl.pathname === '/') {
  // 1. 로그인을 했던 사용자에게는 메인페이지 접근 시 본인 카테고리 목록 조회 페이지를 보여줘야한다.
  // rewrite로 URL은 요청경로를 그대로 보여주는데, 실 내용은 '/me/categories'로 보여준다
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
- [참고링크](https://velog.io/@pds0309/nextjs-%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4%EB%9E%80)
- **nextauth 백링크**: [[3. NextAuth.js 사용해보기 3]]
