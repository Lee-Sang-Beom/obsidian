```tsx

// 파일경로: /app/api/[...slug]/route.ts

import { NextRequest, NextResponse } from "next/server";
import { getServerSession } from "next-auth";
import { authOptions } from "@/app/api/auth/[...nextauth]/AuthOptions";
import { getSession } from "next-auth/react";

  
// app 디렉터리 내 프로젝트에서, /api/... 경로로 GET요청을 수행하는 경우 해당 로직을 거침 
export async function GET(req: NextRequest, res: NextResponse) {

  const nextUrl = req.nextUrl;
  const backpath = nextUrl.pathname.replace(/\/api\//g, "/bapi/");
  const reqUrl = process.env.NEXT_PUBLIC_HOST + backpath + nextUrl.search;
  const headers = await getHeaders(req);

  try {

    const res = await fetch(reqUrl, {
      // @ts-ignore
      headers: {
        "X-AUTH-TOKEN": headers["X-AUTH-TOKEN"],
        Cookie: headers.Cookie,
        withCredentials: true,
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

  const reqUrl =

    process.env.NEXT_PUBLIC_HOST + backendPathname + nextUrl.search;

  

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