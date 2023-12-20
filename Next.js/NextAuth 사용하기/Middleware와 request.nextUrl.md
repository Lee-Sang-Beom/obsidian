
```tsx

// 요청발생경로 http://localhost:3000/Sm/SysChemical?page=0&size=10&chemSearchType=ALL&searchKeyWord=&changwonYn=Y&sort=regDt&orderBy=DESC&active=0
export default withAuth(
  function middleware(request: NextRequest) {
    const requestHeaders = new Headers(request.headers);
    requestHeaders.set("x-url", request.url);  


	// http://localhost:3000/Sm/SysChemical?page=0&size=10&chemSearchType=ALL&searchKeyWord=&changwonYn=Y&sort=regDt&orderBy=DESC&active=0
    console.log("request.url is ", request.url);

	/**
	* 객체
	*/
    console.log("request.nexturl is ", request.nextUrl);

    return NextResponse.next({
      request: {
        headers: requestHeaders,
      },
    });
  },

  // middleware의 callbacks는 요청이 있을때마다, authorized가 실행됨
  // token !== null 이면 로그인된상태임
  {
    callbacks: {
      authorized: ({ req, token }) => {
        if (req.url.includes("/Sm")) {
          /**
           * Sm 페이지
           *  - 일단 로그인 되어야함
           *  - 특정 페이지는 관리자만 접속 가능하거나, 담당자 및 관리자만 접속 가능
           */
          if (token) {
            if (
              req.url.includes("/Sm/UserPermission") ||
              req.url.includes("/Sm/SysAccessIp") ||
              req.url.includes("/Sm/SysLog")
            ) {
              // 사용자권한관리, 접근ip관리, 시스템이력 페이지는 관리자만 접근가능
              if (
                !token.user ||
                !token.user.authList ||
                !token.user.authList.length ||
                !token.user.authList[0].authrtNm
              ) {
                return false;
              }
  
              // 관리자인지 아닌지 검사하여, 관리자인 경우 true반환 (관리자로 로그인었고, middleware에서 해당 요청을 수락)
              return token.user?.authList[0]?.authrtNm === "관리자";
            } else {
              // 화학물질정보관리,화학물질 취급업체관리,대피소 정보관리는 로그인되었지만 검사.
              // 즉, 로그인만되어있으면(token !== null) 되는 페이지임
              return true;
            }
          } else {
            // Sm 페이지는 기본적으로 token!==null인, 로그인이 된 상태여야함.
            // 로그인이 안된상태면 해당 페이지와 관련된 요청을 거부하여, 자동으로 extauth내 AuthOption의 signIn 경로로 정의된 페이지로 이동시켜버림
            return false;
          }
        } else if (req.url.includes("/Et/MyPage")) {
          if (token) {
            return true;
          } else {
            return false;
          }
        } else if (req.url.includes("/Ca")) {
          if (token) {
            return true;
          } else {
            return false;
          }
        } else if (req.url.includes("/Ma")) {
          if (!token) {
            return true;
          } else {
            return false;
          }
        } else {
          return true;
        }
      },
    },
  }
);
```