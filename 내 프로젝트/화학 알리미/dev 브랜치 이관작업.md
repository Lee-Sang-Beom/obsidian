
#### 작업

1. `api` 주소문자열을 감싸고, 실제 요청될 API BaseURL을 반환하는 함수를 만들고, 모든 클라이언트 요청 영역 적용
	- 단, (`serversidefetch` 제외)
	- `main`: `cwchemical` 추가
	- `dev, localhost`: `cwchemical` 추가
```tsx
export function getVaildApiAddrString(value: string): string {
  if (window && window.location.href) {
    if (
      window.location.href.includes("localhost") ||
      window.location.href.includes("http://cca.lksmart.co.kr")
    ) {
      // 검사 1. 로컬이면서 /cwchemical이 들어가있으면? 빼야함
      // 검사 2. 로컬이면서 /cwchemical이 안들어가있으면? 그냥 리턴
      if (value.includes("/cwchemical")) {
        return value.replaceAll("/cwchemical", "");
      } else {
        return value;
      }
    } else {
      // 검사 1. 로컬이 아니면서 /cwchemical이 들어가있으면? 그냥리턴
      // 검사 2. 로컬이 아니면서 /cwchemical이 안들어가있으면? 추가해야함
      if (value.includes("/cwchemical")) {
        return value;
      } else {
        return `/cwchemical${value}`;
      }
    }
  } else {
    // 만약 없으면 그냥 value 리턴해야함
    return value;
  }
}
```

2. 전체 디렉터리 구조 변경 `root` 하위 `cwchemical` 추가
	- `app`
	- `public`

3. `sessionProvider` `basepath`변경
```tsx
"use client";

import React, { use, useEffect, useLayoutEffect, useState } from "react";
import { SessionProvider } from "next-auth/react";

export default function NextAuthSessionProvider({
  children,
}: {
  children: React.ReactNode;
}) {
  // dev환경일 때 : /cwchemical/api/auth
  // local환경일 때 : /api/auth
  const [basePath, setBasePath] = useState<string>("/cwchemical/api/auth");
  useLayoutEffect(() => {
    if (window) {
      if (
        window.location.href.includes("localhost") ||
        window.location.href.includes("http://cca.lksmart.co.kr")
      )
        setBasePath("/api/auth");
    }
  }, []);

  return <SessionProvider basePath={basePath}>{children}</SessionProvider>;
}
```

4. `dev` branch: `assetPrefix` 제거
```ts
const path = require("path");

/** @type {import('next').NextConfig} */
const nextConfig = {
  // experimental: {
  //   appDir: true,
  // },
  sassOptions: {
    includePaths: [path.join(__dirname, "styles")],
  },
  images: {
    domains: ["http://cca.lksmart.co.kr"],
    remotePatterns: [
      {
        protocol: "http",
        hostname: "localhost",
      },
      {
        protocol: "http",
        hostname: "cca.lksmart.co.kr",
      },
      {
        protocol: "https",
        hostname: "cca.lksmart.co.kr",
      },
    ],
  },

  // 라이브에서만 추가
  // basePath: "/cwchemical",
  // assetPrefix: "/cwchemical/",

  output: "standalone",
};

module.exports = nextConfig;

```
6. `api key (vworld)` 변경
7. 로그아웃 및 헤더 로직 변경
```tsx
"use client";

import style from "./header.module.scss";
import Link from "next/link";
import GnbFullDrop from "../GnbFullDrop/GnbFullDrop";
import HamburgerBar from "../HamburgerBar/HamburgerBar";
import { SetStateAction, useEffect, useLayoutEffect, useState } from "react";
import { usePathname } from "next/navigation";
import { BiLockOpen } from "react-icons/bi";
import { FiUser } from "react-icons/fi";
import { Session } from "next-auth";
import { getIsLoginUser } from "@/utils/user";
import { signIn, signOut } from "next-auth/react";
import { useRouter } from "next/navigation";
import { MenuTreeResponse, TablePageResponse } from "@/types/common/commonType";
import Dialog from "../Dialog/Dialog";
import Input from "../Input/Input";
import { useAutoAlert } from "@/hooks/Alert/useAutoAlert";
import { HiOutlineLightBulb } from "react-icons/hi";
import { BoardInfoResponse } from "@/types/board/BoardType";
import AlertSlide from "../slider/AlertSlide";
import { getVaildApiAddrString } from "@/utils/utils";
interface HeaderProps {
  // TODO: type 변경
  menus: MenuTreeResponse[];
  session: Session | null;
  hostUrl: string;
  alertList: TablePageResponse<BoardInfoResponse[]>;
}

/**
 *
 * @menus 전체 메뉴
 * @type 헤어 타입 - 기본 서브 / 메인으로 설정 시 기본 bg가 블랙으로 변경 되고 호버 시 화이트로 변경됨
 */
export default function Header({
  menus,
  session,
  hostUrl,
  alertList,
}: HeaderProps) {
  const [pathname, setPathname] = useState<string | null>(null);

  // gnb hover
  const [gnbHover, setGnbHover] = useState<boolean>(false);

  const [baseUrl, setBaseUrl] = useState<string>("");
  const router = useRouter();

  // 로그인 관련 정보
  const [userId, setUserId] = useState<string>("");
  const [userPw, setUserPw] = useState<string>("");

  const [isLoginUser, setIsLoginUser] = useState(getIsLoginUser(session));

  const [loginPopup, setLoginPopup] = useState<boolean>(false);
  const { setIsChange, setText, setStatus } = useAutoAlert();
  //const [windowHref, setWindowHref] = useState<string | null>(null);

  useLayoutEffect(() => {
    setIsLoginUser(getIsLoginUser(session));

    if (window) {
      setPathname(window.location.href || null);
      if (window.location && window.location.href) {
        if (window.location.href.includes("localhost")) {
          setBaseUrl("http://localhost:3000/cwchemical");
        } else {
          setBaseUrl(process.env.NEXT_PUBLIC_BASE_URL!);
        }
      }
    } else {
      setBaseUrl(process.env.NEXT_PUBLIC_BASE_URL!);
    }
  }, []);

  // 로그인 되어있는 상태
  if (isLoginUser) {
    return (
      <header
        id="header"
        className={`${style.header} ${
          (pathname === null ||
            pathname === "http://localhost:3000/cwchemical" ||
            pathname === "http://cca.lksmart.co.kr/cwchemical" ||
            pathname === "https://www.changwon.go.kr/cwchemical" ||
            pathname === "https://changwon.go.kr/cwchemical") &&
          gnbHover === false
            ? style.main
            : ""
        }`}
        style={{
          backgroundColor:
            (pathname === null ||
              pathname === "http://localhost:3000/cwchemical" ||
              pathname === "http://cca.lksmart.co.kr/cwchemical" ||
              pathname === "https://www.changwon.go.kr/cwchemical" ||
              pathname === "https://changwon.go.kr/cwchemical") &&
            gnbHover === false
              ? "#0000001a"
              : "var(--m-100)",
          color:
            (pathname === null ||
              pathname === "http://localhost:3000/cwchemical" ||
              pathname === "http://cca.lksmart.co.kr/cwchemical" ||
              pathname === "https://www.changwon.go.kr/cwchemical" ||
              pathname === "https://changwon.go.kr/cwchemical") &&
            gnbHover === false
              ? "var(--m-100)"
              : "",
          position:
            pathname === null ||
            pathname === "http://localhost:3000/cwchemical" ||
            pathname === "http://cca.lksmart.co.kr/cwchemical" ||
            pathname === "https://www.changwon.go.kr/cwchemical" ||
            pathname === "https://changwon.go.kr/cwchemical"
              ? "absolute"
              : "relative",
        }}
      >
        <div className={`container ${style.container}`}>
          <div className={style.left}>
            {/* 로고 */}
            <h1 className="logo">
              <a href="/cwchemical/" title="홈으로 이동">
                창원산단 화학물질정보
                <br />
                알리미 서비스 시스템
              </a>
            </h1>

            <div
              onMouseOver={() => {
                setGnbHover(true);
              }}
              onMouseLeave={() => {
                setGnbHover(false);
              }}
              id="topmenu_wrap"
            >
              <GnbFullDrop session={session} menus={menus} />
            </div>
          </div>

          <div className={style.t_btn_r}>
            {/* 로그인 시 생성 */}
            <button
              className="tbtn_login"
              type="button"
              title="로그아웃"
              style={{
                color:
                  (pathname === null ||
                    pathname === "http://localhost:3000/cwchemical" ||
                    pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                    pathname === "https://www.changwon.go.kr/cwchemical" ||
                    pathname === "https://changwon.go.kr/cwchemical") &&
                  gnbHover === false
                    ? "var(--m-100)"
                    : "",
                outlineColor:
                  (pathname === null ||
                    pathname === "http://localhost:3000/cwchemical" ||
                    pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                    pathname === "https://www.changwon.go.kr/cwchemical" ||
                    pathname === "https://changwon.go.kr/cwchemical") &&
                  gnbHover === false
                    ? "var(--m-100)"
                    : "",
              }}
              onClick={async (e) => {
                e.preventDefault();

                fetch(getVaildApiAddrString(`/cwchemical/api/member/logout`), {
                  method: "POST",
                  body: JSON.stringify({
                    seq: session?.user.userSeq,
                    userId: session?.user.userId,
                  }),
                })
                  .then((res) => res.json())
                  .then(() => {
                    signOut({ redirect: false }).then(() => {
                      // router.refresh();
                      window.location.href = "/cwchemical";
                    });
                  });
              }}
            >
              <BiLockOpen size={25} role="img" aria-label="자물쇠이미지" />
              로그아웃
            </button>

            {/* 분리선 */}
            <span className="t_btn_line">|</span>

            {/* 마이페이지 */}
            <button
              className="tbtn_login"
              type="button"
              title="마이페이지로 이동"
              onClick={() => {
                router.push("/cwchemical/Et/MyPage");
              }}
              style={{
                color:
                  (pathname === null ||
                    pathname === "http://localhost:3000/cwchemical" ||
                    pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                    pathname === "https://www.changwon.go.kr/cwchemical" ||
                    pathname === "https://changwon.go.kr/cwchemical") &&
                  gnbHover === false
                    ? "var(--m-100)"
                    : "",
                outlineColor:
                  (pathname === null ||
                    pathname === "http://localhost:3000/cwchemical" ||
                    pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                    pathname === "https://www.changwon.go.kr/cwchemical" ||
                    pathname === "https://changwon.go.kr/cwchemical") &&
                  gnbHover === false
                    ? "var(--m-100)"
                    : "",
              }}
            >
              <FiUser size={25} role="img" aria-label="유저 아이콘" />
              마이페이지
            </button>
            <span className="t_btn_line">|</span>

            <HamburgerBar
              login={isLoginUser}
              menus={menus}
              openType="basic"
              type="left"
              propsStyle={{
                outlineColor:
                  (pathname === null ||
                    pathname === "http://localhost:3000/cwchemical" ||
                    pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                    pathname === "https://www.changwon.go.kr/cwchemical" ||
                    pathname === "https://changwon.go.kr/cwchemical") &&
                  gnbHover === false
                    ? "var(--m-100)"
                    : "",
              }}
              headerType={
                (pathname === null ||
                  pathname === "http://localhost:3000/cwchemical" ||
                  pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                  pathname === "https://www.changwon.go.kr/cwchemical" ||
                  pathname === "https://changwon.go.kr/cwchemical") &&
                gnbHover === false
                  ? "main"
                  : undefined
              }
              hover={true}
              text="전체메뉴"
            />
          </div>
        </div>
      </header>
    );
  } else {
    // 로그인 안 된 상태
    return (
      <header
        id="header"
        className={`${style.header_bar} ${
          (pathname === null ||
            pathname === "http://localhost:3000/cwchemical" ||
            pathname === "http://cca.lksmart.co.kr/cwchemical" ||
            pathname === "https://www.changwon.go.kr/cwchemical" ||
            pathname === "https://changwon.go.kr/cwchemical") &&
          gnbHover === false
            ? style.main
            : (pathname === null ||
                pathname === "http://localhost:3000/cwchemical" ||
                pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                pathname === "https://www.changwon.go.kr/cwchemical" ||
                pathname === "https://changwon.go.kr/cwchemical") &&
              gnbHover === true
            ? `${style.hover} ${style.main}`
            : ""
        }`}
        style={{
          background:
            (pathname === null ||
              pathname === "http://localhost:3000/cwchemical" ||
              pathname === "http://cca.lksmart.co.kr/cwchemical" ||
              pathname === "https://www.changwon.go.kr/cwchemical" ||
              pathname === "https://changwon.go.kr/cwchemical") &&
            gnbHover === false
              ? ""
              : "unset",
          backgroundColor:
            (pathname === null ||
              pathname === "http://localhost:3000/cwchemical" ||
              pathname === "http://cca.lksmart.co.kr/cwchemical" ||
              pathname === "https://www.changwon.go.kr/cwchemical" ||
              pathname === "https://changwon.go.kr/cwchemical") &&
            gnbHover === false
              ? "#0000001a"
              : "var(--gray-100)",
          color:
            (pathname === null ||
              pathname === "http://localhost:3000/cwchemical" ||
              pathname === "http://cca.lksmart.co.kr/cwchemical" ||
              pathname === "https://www.changwon.go.kr/cwchemical" ||
              pathname === "https://changwon.go.kr/cwchemical") &&
            gnbHover === false
              ? "var(--m-100)"
              : "",
          position:
            pathname === null ||
            pathname === "http://localhost:3000/cwchemical" ||
            pathname === "http://cca.lksmart.co.kr/cwchemical" ||
            pathname === "https://www.changwon.go.kr/cwchemical" ||
            pathname === "https://changwon.go.kr/cwchemical"
              ? "absolute"
              : "relative",
        }}
      >
        <div className={`container ${style.container}`}>
          <div className={style.left}>
            {/* 로고 */}
            <h1 className="logo">
              <a href="/cwchemical/" title="홈으로 이동">
                창원산단 화학물질정보
                <br />
                알리미 서비스 시스템
              </a>
            </h1>

            <div
              onMouseOver={() => {
                setGnbHover(true);
              }}
              onMouseLeave={() => {
                setGnbHover(false);
              }}
              id="topmenu_wrap"
            >
              <GnbFullDrop session={session} menus={menus} />
            </div>
          </div>
          <div className={style.t_notice}>
            <div className={style.noti_text}>
              <HiOutlineLightBulb size={25} />
              <div>화학물질 대피요령</div>
            </div>
            <AlertSlide
              data={alertList && alertList.content ? alertList.content : []}
            />
            {/* <span>
              2-메틸프로페인나이트릴 | 이 물질에 급성 노출되었을 경우 혼수상태,
              발작, 두통, 수전증, 중추신경계 자극, 마비, 파킨슨 병 및 그밖에
              후유증이 나타날 수 있음
            </span> */}
          </div>
          <div className={style.t_btn_r}>
            <Dialog
              open={loginPopup}
              width="20%"
              setOpen={(open) => {
                setLoginPopup(open);
              }}
              openTriggerComponent={
                <button
                  className="tbtn_login"
                  type="button"
                  title="로그인하기"
                  // style={{
                  //   color:
                  //     (pathname === null || pathname === "/") &&
                  //     gnbHover === false
                  //       ? "var(--m-100)"
                  //       : "",
                  //   outlineColor:
                  //     (pathname === null || pathname === "/") &&
                  //     gnbHover === false
                  //       ? "var(--m-100)"
                  //       : "",
                  // }}
                >
                  관리자 로그인
                </button>
              }
              title={"로그인"}
            >
              <>
                <div className={style.m_login}>
                  {/* <p className={style.login_tit}>로그인</p> */}
                  <ul className={style.m_login_ul}>
                    <li>
                      <Input
                        labelTitle="아이디"
                        size={"md"}
                        id={"user_id"}
                        value={userId}
                        onChange={(e) => {
                          setUserId(e.currentTarget.value);
                        }}
                        placeholder="아이디 입력"
                      ></Input>
                    </li>
                    <li>
                      <Input
                        id={"user_pw"}
                        size={"md"}
                        type={"password"}
                        labelTitle="비밀번호"
                        placeholder="비밀번호 입력"
                        value={userPw}
                        onChange={(e) => {
                          setUserPw(e.currentTarget.value);
                        }}
                        onKeyDown={async (e) => {
                          if (e.key === "Enter") {
                            if (!userId.length) {
                              setStatus("error");
                              setText("아이디를 입력해주세요.");
                              setIsChange(true);
                              return;
                            }
                            if (!userPw.length) {
                              setStatus("error");
                              setText("비밀번호를 입력해주세요.");
                              setIsChange(true);
                              return;
                            }

                            const result = await signIn("credentials", {
                              userId: userId,
                              userPswd: userPw,
                              redirect: false,
                            });

                            if (result) {
                              if (result.status == 200) {
                                // 지도 출력 시, 완전히 페이지가 새로고침되어야 vworld map이 출력되기 때문에 router 미사용
                                window.location.href = "/cwchemical/";
                                // router.push("/");
                                // router.refresh();
                              } else {
                                setText(
                                  "아이디와 비밀번호를 다시 확인해주세요."
                                );
                                setStatus("error");
                                setIsChange(true);
                              }
                            }
                          }
                        }}
                      />
                    </li>
                  </ul>

                  {/* 공통엔 없는 색상+메인에서만 사용하는 색상이므로 따로 일반 버튼 태그 사용 */}
                  <button
                    type="button"
                    className={style.btn_login}
                    title={"로그인"}
                    onClick={async (e) => {
                      e.preventDefault();

                      if (!userId.length) {
                        setStatus("error");
                        setText("아이디를 입력해주세요.");
                        setIsChange(true);
                        return;
                      }
                      if (!userPw.length) {
                        setStatus("error");
                        setText("비밀번호를 입력해주세요.");
                        setIsChange(true);
                        return;
                      }

                      const result = await signIn("credentials", {
                        userId: userId,
                        userPswd: userPw,
                        redirect: false,
                      });

                      if (result) {
                        if (result.status == 200) {
                          window.location.href = "/cwchemical/";

                          // router.push("/");
                          // router.refresh();
                        } else {
                          setText("아이디와 비밀번호를 다시 확인해주세요.");
                          setStatus("error");
                          setIsChange(true);
                        }
                      }
                    }}
                  >
                    로그인
                  </button>
                  <div className={style.login_bot}>
                    <span className={style.al_right}>
                      <Link
                        href={"/cwchemical/Ma/findId"}
                        prefetch={false}
                        title="아이디 찾기 페이지 이동"
                        onClick={() => {
                          setLoginPopup(false);
                        }}
                      >
                        아이디 찾기
                      </Link>
                      <span>|</span>
                      <Link
                        href={"/cwchemical/Ma/findPw"}
                        prefetch={false}
                        title="비밀번호 찾기 페이지 이동"
                        onClick={() => {
                          setLoginPopup(false);
                        }}
                      >
                        비밀번호 찾기
                      </Link>
                    </span>
                  </div>
                </div>
              </>
            </Dialog>
            {/* 분리선 */}
            <span className="t_btn_line">|</span>
            {/* 로그인 팝업 */}

            <HamburgerBar
              login={isLoginUser}
              menus={menus}
              openType="basic"
              type="left"
              propsStyle={{
                outlineColor:
                  (pathname === null ||
                    pathname === "http://localhost:3000/cwchemical" ||
                    pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                    pathname === "https://www.changwon.go.kr/cwchemical" ||
                    pathname === "https://changwon.go.kr/cwchemical") &&
                  gnbHover === false
                    ? "var(--m-100)"
                    : "",
              }}
              headerType={
                (pathname === null ||
                  pathname === "http://localhost:3000/cwchemical" ||
                  pathname === "http://cca.lksmart.co.kr/cwchemical" ||
                  pathname === "https://www.changwon.go.kr/cwchemical" ||
                  pathname === "https://changwon.go.kr/cwchemical") &&
                gnbHover === false
                  ? "main"
                  : undefined
              }
              hover={true}
              text="전체메뉴"
            />
          </div>
        </div>
      </header>
    );
  }
}
```


[중요]
#### 완료
1. sessionProvider basePath 변경
2. app 하위요소 및 이미지 접근경로 cwchemical 추가
3. 로컬 및 dev환경 `assertPrefix` 삭제
4. `api key`