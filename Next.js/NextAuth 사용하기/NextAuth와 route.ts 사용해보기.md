```tsx

// 파일경로: /app/api/[...slug]/route.ts

import { NextRequest, NextResponse } from "next/server";
import { getServerSession } from "next-auth";
import { authOptions } from "@/app/api/auth/[...nextauth]/AuthOptions";
import { getSession } from "next-auth/react";

  
// app 디렉터리 내 프로젝트에서, /api/... 경로로 GET요청을 수행하는 경우 해당 로직을 거침 

// 해당 코드는 클라이언트에서 API 요청을 받아, 백엔드 서버로 다시 rediection하고,
// 해당 API 엔드포인트에 대한 데이터를 가져와 클라이언트에 반환하는 것
export async function GET(req: NextRequest, res: NextResponse) {

  // req.nextUrl: 현재 요청의 URL 정보를 저장하고 있는 객체
  const nextUrl = req.nextUrl;
  const backendPathname = nextUrl.pathname.replace(/\/api\//g, "/bapi/");

  // 환경 변수와 변형된 `backendPathname`, `nextUrl.search`를 조합하여 백엔드 서버로 보낼 요청 URL을 생성
  const reqUrl = process.env.NEXT_PUBLIC_HOST + backendPathname + nextUrl.search;
  const headers = await getHeaders(req);

  try {
    const res = await fetch(reqUrl, {
      // @ts-ignore
      headers: {
        "X-AUTH-TOKEN": headers["X-AUTH-TOKEN"],
        Cookie: headers.Cookie, // 요청이 쿠키와 인증 토큰을 포함하도록 함
        withCredentials: true, //  Cross-Origin 요청에서 쿠키와 같은 사용자 인증 정보를 전송하기 위함
      },
    });
    const data = await res.json();
    return NextResponse.json(data);
  } catch (e) {
    console.log(e);
    return NextResponse.json({ error: e });
  }
}

  

export async function POST(req: NextRequest, res: NextResponse) {

  const nextUrl = req.nextUrl;
  const backendPathname = nextUrl.pathname.replace(/\/api\//g, "/bapi/");
  const reqUrl = process.env.NEXT_PUBLIC_HOST + backendPathname + nextUrl.search;


  // `getHeaders` 함수는 사용자 세션 정보를 기반으로 헤더를 생성함.
  // 이것은 사용자의 인증 및 세션 정보를 포함하는 데 사용되며, 서버에서 생성된 헤더를 가져오는 역할을 함
  // const headers = await getHeaders(req, res);


  // req.headers: 현재 요청(`req`)에서 직접 헤더를 추출하여 `headers` 변수에 저장
  // `req.headers`는 클라이언트가 요청한 시점에 설정된 헤더
  // 이것은 클라이언트가 전송한 요청 헤더를 그대로 사용하는 것이며, 이 헤더에는 클라이언트 측에서 추가한 모든 정보가 포함되어 있음
  const headers = req.headers;
  const body = await req.json();

  try {
    const res = await fetch(reqUrl, {
      method: "POST",
      // @ts-ignore
      headers: {
        "Content-Type": "application/json; charset=UTF-8",
        ...headers,
      },
      body: JSON.stringify(body),
    });
    
    const data = await res.json();
    return NextResponse.json(data);
  } catch (e) {
    console.log(e);
    return NextResponse.json({ error: e });
  }
}

// 요청 헤더를 구성하기 위해 사용자 세션에서 가져온 인증 토큰 및 refresh token을 사용하고, `withCredentials`를 `true`로 설정하여 요청이 사용자 인증 정보를 포함하도록 하는 헤더 정보를 담고 있는 객체를 반환
async function getHeaders(req: NextRequest) {
  const session = await getSession();
  return {
     // 사용자의 인증 토큰
     // 세션에서 가져온 정보 중에서 `jwtAuthToken` 속성의 값
    "X-AUTH-TOKEN": session?.user.jwtAuthToken,
    
     // 쿠키 정보
     // `"X-REFRESH_TOKEN=" + session?.user.jwtRefreshToken`를 사용하여 refresh token을 쿠키에 추가
    Cookie: "X-REFRESH_TOKEN=" + session?.user.jwtRefreshToken,
    
    // 이 헤더를 포함한 요청은 사용자 인증 정보를 포함하도록 설정
    withCredentials: true,
  };
}

```