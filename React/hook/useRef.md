#### 1. `useRef`란?

- 함수형 컴포넌트에서, `useRef`를 부르면, **ref object**를 반환해준다.
	- `useRef`를 호출하면서 인자로 전달한 value값은, ref object의 `current`에 저장된다.
	- `ref.current`는 언제나 접근 및 수정이 가능하다.

- 반환된 `ref`는 컴포넌트의 **전 생명주기**동안 유지된다.
	- 컴포넌트가 계속해서 렌더링되어도, 컴포넌트가 **언마운트**되기 전까지는 `ref`의 값이 그대로 유지된다.
```jsx
// ref object 생성
const ref = useRef(value);

// ref.current에 언제든 접근 및 수정 가능
ref.current = newValue; 
```


#### 2. 언제 쓸까?

- `ref`는 `state`와 비슷하게 **어떤 값을 저장하는 저장 공간**으로 사용된다.
	- `state`가 변화하면, 자동으로 컴포넌트는 재랜더링된다. 
	- 이 때, 함수형 컴포넌트는 **함수**이기 때문에, 리렌더링 발생 시 함수가 다시 불러와지며, 내부의 변수는 다시 초기화되어 버린다.
		- 그래서 가끔 원하지 않는 렌더링으로 곤란해질 때가 있다.

- 반면, `ref`는 아무리 변경되어도 컴포넌트가 재렌더링되지 않기 때문에, 불필요한 렌더링을 막을 수 있다.
	- 또한, 컴포넌트가 아무리 렌더링되어도 `ref` 안에 저장된 값은 계속 유지된다.
	- 따라서 변경 시 **렌더링을 발생시키지 말아야하는 값을 다룰 때 편리**하다.

- `ref`를 통해 **DOM요소에 접근**할 수도 있다. 
	- 예시) input요소를 클릭하지 않아도 focus를 줄 때

```jsx
import logo from './logo.svg';
import './App.css';
import {useState, useRef, useEffect} from "react";

function App() {

  useEffect (()=>{
    inputRef.current.focus(); // 페이지 로딩 시, 자동 focus
  },[])

  const [count, setCount] = useState(0); // count와 ref차이를 비교 : 렌더링 유무
  const [values, setValues] = useState("");

  const stateRef = useRef(0); // count와 비교할 목적의 ref초기화
  const inputRef = useRef(); // input태그 자동 focus를 위한 ref (DOM)

  const countOut =()=>{ // count 증가
    setCount(count+1);
    console.log(`count = ${count}`);
  }

  const refOut = () => { // ref 증가
    stateRef.current += 1;
    console.log(`ref = ${stateRef.current}`);
  }

  const login = ()=>{
    alert(`어서오세요, ${inputRef.current.value}`)
  }

  return (
    <div>
      <p>count: {count}</p>
      <p>ref : {stateRef.current} </p>
      <button onClick={countOut}>add count</button>
      <button onClick={refOut}>add count</button>

      <div>
      <input ref={inputRef} type="text"/>
      <button onClick={login}>he</button>
      </div>
    </div>
  );
}

export default App;
```


#### 3. 프로젝트 내 사용경험 1 (모바일 좌우 스크롤)

- `ref`는 위에서 언급했듯 **DOM**에 접근할 수 있는 인터페이스적 역할을 수행해주기 때문에 각종 `element`를 관리하고 추적하는 데에 용이하다.

- 아래 작업은 `ref`를 `<a>`태그에 부여해 active Link가 무엇인지를 탐색하고, 현재 경로(`pathNm`)와 메뉴리스트 데이터를 이용하여 모바일에서 페이지 로드 시, `depth3` 선택 메뉴가 바로 보이게끔 좌우스크롤을 구현한 것이다. 

```tsx
"use client";

import React, { useEffect, useRef, useState } from "react";
import style from "./SubTopMenu.module.scss";
import "./SubMenu.css";
import { GoHome } from "react-icons/go";
import { GrFormDown, GrFormNext, GrFormUp } from "react-icons/gr";
import { useParams, usePathname, useRouter } from "next/navigation";
import * as Select from "@radix-ui/react-select";
import classnames from "classnames";
import { AuthMenuTreeResponse } from "@/types/common/MenuType";
import { menuByDepth } from "@/utils/utils";
import { useSearchParams } from "next/navigation";
import { User } from "next-auth";

interface SubTopMenuProps {
  menuData: AuthMenuTreeResponse[];
  curUrl: string;
  user: User | null;
}
export default function SubTopMenu({
  menuData,
  curUrl,
  user,
}: SubTopMenuProps) {
  // 필요 변수
  const pathNm = usePathname();
  const [pathNmClass, setPathNmClass] = useState("/");
  const router = useRouter();
  const { depth1Menu, depth2Menu, depth3Menu } = menuByDepth(menuData);

  const param = useSearchParams();

  const [isMounted, setIsMounted] = useState<boolean>(false);
  const [selectDepth1Menu, setSelectDepth1Menu] =
    useState<AuthMenuTreeResponse>();
  const [selectDepth2Menu, setSelectDepth2Menu] =
    useState<AuthMenuTreeResponse>();

  const [selectDepth3Menu, setSelectDepth3Menu] =
    useState<AuthMenuTreeResponse>();

  const [selectRootValue, setSelectRootValue] = useState<string>("");
  const [selectSubRootValue, setSelectSubRootValue] = useState<string>("");

  const [activeMenuIndex, setActiveMenuIndex] = useState<number>(-1);
  const activeRef = useRef<any | null>(null);

  // ...

  useEffect(() => {
    if (activeRef.current) {
      const parentElement = document.querySelector(
        `.${style.scroll_box}`
      ) as HTMLElement;

      const left = activeRef.current.offsetLeft - 10;
      parentElement.style.scrollBehavior = "smooth";
      parentElement.scrollLeft = left;
    }
  }, [activeMenuIndex]);

  useEffect(() => {
    if (!selectDepth2Menu) return;

    if (menuData && menuData.length) {
      if (pathNm && pathNm.includes("/searchtotal")) {
      } else {
        depth3Menu
          .filter((depth3, idx) => {
            return depth3.upMenuSeq === selectDepth2Menu?.menuSeq;
          })
          .sort(
            (a: AuthMenuTreeResponse, b: AuthMenuTreeResponse) =>
              a.sortNo - b.sortNo
          )
          .map((item, idx) => {
            if (pathNm === item.menuUrl) {
              setActiveMenuIndex(idx);
            }
          });
      }
    }
  }, [selectDepth2Menu]);
  
  return (
    <>
      {/* ... */}
      {pathNm?.includes("/lg") || pathNm?.includes("/searchtotal") ? (
        <></>
      ) : (
        <div>
          {/* ... */}
          {pathNm && pathNm.includes("/searchtotal") ? (
            <></>
          ) : (
            <div className={style.scroll_box}>
              <div className={style.depth_3_box}>
                {depth3Menu.map((depth3) => {
                  if (depth3.upMenuSeq === selectDepth2Menu?.menuSeq) {
                    return (
                      <a
                        href={depth3.menuUrl}
                        key={`3차_${depth3.menuSeq}_${Math.random()}`}
	                    
	                    {/* 현재 active 경로와 일치하는 a태그만 active를 부여 - className도 같음 */}
                        ref={
                          depth3.menuUrl.includes(
                            pathNm?.split("/").length
                              ? pathNm?.split("/").length < 5
                                ? pathNm?.split("/")[
                                    pathNm?.split("/").length - 1
                                  ]
                                : pathNm?.split("/")[
                                    pathNm?.split("/").length - 2
                                  ]
                              : ""
                          ) ||
                          (pathNm?.includes("wis") &&
                            depth3.menuUrl.includes(
                              pathNm?.split("/")[
                                pathNm?.split("/").length - 2
                              ] || ""
                            )) ||
                          (pathNm?.includes("wis/") &&
                            depth3.menuUrl.includes(
                              pathNm?.split("/")[
                                pathNm?.split("/").length - 3
                              ] || ""
                            ))
                            ? activeRef
                            : null
                        }
                        className={
                          depth3.menuUrl.includes(
                            pathNm?.split("/").length
                              ? pathNm?.split("/").length < 5
                                ? pathNm?.split("/")[
                                    pathNm?.split("/").length - 1
                                  ]
                                : pathNm?.split("/")[
                                    pathNm?.split("/").length - 2
                                  ]
                              : ""
                          ) ||
                          (pathNm?.includes("wis") &&
                            depth3.menuUrl.includes(
                              pathNm?.split("/")[
                                pathNm?.split("/").length - 2
                              ] || ""
                            )) ||
                          (pathNm?.includes("wis/") &&
                            depth3.menuUrl.includes(
                              pathNm?.split("/")[
                                pathNm?.split("/").length - 3
                              ] || ""
                            ))
                            ? style.active
                            : ""
                        }
                      >
                        {depth3.menuNm}
                      </a>
                    );
                  } else if (
                    pathNm?.includes("notice") ||
                    param?.get("type") === "3" ||
                    param?.get("type") === "1"
                  ) {
                    if (
                      depth3.menuUrl === "/board?type=1" ||
                      depth3.menuUrl.includes("type=3") ||
                      depth3.menuUrl.includes("/notice/")
                    )
                      return (
                        <a
                          href={depth3.menuUrl}
                          key={`공지사항_${depth3.menuSeq}_${Math.random()}`}
                          className={
                            depth3.menuUrl.includes(
                              param?.get("type") || pathNm || ""
                            )
                              ? style.active
                              : ""
                          }
                          
	                      {/* 현재 active 경로와 일치하는 a태그만 active를 부여 - className도 같음 */}
                          ref={
                            depth3.menuUrl.includes(
                              param?.get("type") || pathNm || ""
                            )
                              ? activeRef
                              : null
                          }
                        >
                          {depth3.menuNm}
                        </a>
                      );
                  } else if (
                    param?.get("type") === "5" ||
                    param?.get("type") === "6" ||
                    param?.get("type") === "7" ||
                    param?.get("type") === "8" ||
                    param?.get("type") === "9" ||
                    param?.get("type") === "10"
                  ) {
                    if (
                      depth3.menuUrl.includes("type=5") ||
                      depth3.menuUrl.includes("type=6") ||
                      depth3.menuUrl.includes("type=7") ||
                      depth3.menuUrl.includes("type=8") ||
                      depth3.menuUrl.includes("type=9") ||
                      depth3.menuUrl.includes("type=10")
                    ) {
                      return (
                        <a
                          href={depth3.menuUrl}
                          key={`사업단_${depth3.menuSeq}_${Math.random()}`}
                          className={
                            depth3.menuUrl.includes(param.get("type") || "")
                              ? style.active
                              : ""
                          }
                          
                          {/* 현재 active 경로와 일치하는 a태그만 active를 부여 - className도 같음 */}
                          ref={
                            depth3.menuUrl.includes(param.get("type") || "")
                              ? activeRef
                              : null
                          }
                        >
                          {depth3.menuNm}
                        </a>
                      );
                    }
                  } else {
                    return <></>;
                  }
                })}
              </div>
            </div>
          )}
        </div>
      )}
    </>
  );
}

// ...

```

- **페이지 로드** (스크롤 이동 전)
	- 현재 선택된 3차메뉴가 무엇인지 한 눈에 보여지지 않음.
![[예제 1. 스크롤 전.png]]

- **페이지 로드** (스크롤 이후)
	- 페이지 로드 후, 자동으로 3차메뉴 리스트 중 선택된 메뉴가 무엇인지 탐색한 후, 자동 스크롤을 진행함
	- 이렇게 하면, UX적으로 사용자가 선택한 메뉴가 무엇인지 확인 가능함
![[예제 1. 스크롤 후.png]]


#### 4. 프로젝트 내 사용경험 2 (프린트)

- 특정 `element`에 `ref`를 부여하면, 해당 `element` 영역을 인쇄할 수 있다.
```tsx
"use client";

import Style from "./page.module.css";
import Link from "next/link";
import { useAutoAlert } from "@/hooks/alert/useAutoAlert";
import { useRouter } from "next/navigation";
import { HiOutlineArrowLongRight } from "react-icons/hi2";
import { IoIosArrowDown } from "react-icons/io";
import SectionOnScroll from "@/components/common/SectionPopupOnScroll/SectionOnScroll";
import { RiPrinterLine } from "react-icons/ri";
import { Button } from "@/components/common/Button/Button";
import moment from "moment";
import { VscFilePdf } from "react-icons/vsc";
import html2canvas from "html2canvas";
import { useReactToPrint } from "react-to-print";
import jsPDF from "jspdf";
import { useEffect, useRef, useState } from "react";
export default function MireGreet() {
  const divRef = useRef<HTMLDivElement>(null);
  const { setIsChange, setText, setStatus } = useAutoAlert();

  const onPrint = useReactToPrint({
    content: () => divRef.current,
  });

  function scrollToBottom() {
    if (window) {
      // 문서의 높이
      const documentHeight = document.documentElement.scrollHeight;

      // 현재 창의 높이
      const windowHeight = window.innerHeight;

      // 스크롤할 거리 계산
      const scrollDistance = documentHeight - windowHeight;

      // 맨 아래로 스크롤
      window.scrollTo({
        top: scrollDistance,
        left: 0,
        behavior: "smooth", // 부드러운 스크롤을 원하면 'smooth' 사용
      });
    }
  }

  return (
    <>
      <div className={Style.print_none}>
        <Button
          title={"PDF다운로드"}
          btnColor={"white"}
          btnSize={"md"}
          btnStyle={"br_3"}
          onClick={() => {
            setIsChange(true);
            setStatus("info");
            setText("PDF 파일을 준비하고 있습니다. 잠시만 기다려주세요...");

            scrollToBottom();

            setTimeout(async () => {
              if (divRef.current) {
                const canvas = await html2canvas(divRef.current, {
                  useCORS: true,
                  logging: true,
                });
                const imageFile = canvas.toDataURL("image/png");
                const doc = new jsPDF("p", "mm", "a4");
                const pageWidth = doc.internal.pageSize.getWidth();
                const pageHeight = doc.internal.pageSize.getHeight();

                const widthRatio = pageWidth / canvas.width;
                const customHeight = canvas.height * widthRatio;
                doc.addImage(imageFile, "png", 0, 0, pageWidth, customHeight);
                let heightLeft = customHeight;
                let heightAdd = -pageHeight;

                while (heightLeft >= pageHeight) {
                  doc.addPage();
                  doc.addImage(
                    imageFile,
                    "png",
                    0,
                    heightAdd,
                    pageWidth,
                    customHeight
                  );
                  heightLeft -= pageHeight;
                  heightAdd -= pageHeight;
                }

                doc.save(
                  `버추얼 기술소개_${moment().format("YYYY-MM-DD")}.pdf`
                );
              }
            }, 1000);
          }}
        >
          <VscFilePdf role="img" aria-label="pdf 아이콘" />
          <span className={Style.mo_none}>PDF다운로드</span>
        </Button>
        <div className={Style.mo_none}>
          <Button
            title={"프린트"}
            btnColor={"white"}
            btnSize={"md"}
            btnStyle={"br_3"}
            onClick={() => {
              scrollToBottom();
              setTimeout(() => {
                onPrint();
              }, 1000);
            }}
          >
            <RiPrinterLine role="img" aria-label="프린트 아이콘" />
            프린트
          </Button>
        </div>
      </div>

	  {/* .greet_wrap 기준으로, divRef 부여: 해당 영역 인쇄가능 */}
      <div className={Style.greet_wrap} ref={divRef}>
        <SectionOnScroll>
          {/* ... */}
        </SectionOnScroll>
        <SectionOnScroll>
          {/* ... */}
        </SectionOnScroll>
        <SectionOnScroll>
          {/* ... */}
        </SectionOnScroll>
        <SectionOnScroll>
          {/* ... */}
        </SectionOnScroll>
        <SectionOnScroll>
          {/* ... */}
        </SectionOnScroll>
      </div>
    </>
  );
}

```

![[예제 2. 프린트.png]]

