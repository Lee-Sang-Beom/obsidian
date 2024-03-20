- https://velog.io/@pds0309/nextjs-%EB%AF%B8%EB%93%A4%EC%9B%A8%EC%96%B4%EB%9E%80

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
