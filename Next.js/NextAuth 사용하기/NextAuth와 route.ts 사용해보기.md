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

  // const headers = await getHeaders(req, res);
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

async function getHeaders(req: NextRequest) {
  const session = await getSession();

  return {
    "X-AUTH-TOKEN": session?.user.jwtAuthToken,
    Cookie: "X-REFRESH_TOKEN=" + session?.user.jwtRefreshToken,
    withCredentials: true,
  };

}

```