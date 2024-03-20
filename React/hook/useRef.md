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

      <div className={Style.greet_wrap} ref={divRef}>
        <SectionOnScroll>
          <section className={Style.greet_box1}>
            <ul className={Style.top_info}>
              <p className={Style.slogan1}>
                버추얼 기반 미래차 부품 고도화 사업을 통해
              </p>
              <p className={Style.slogan2}>
                미래차 사업전환을 촉진하며, 산업 생태계 구축에 기여하겠습니다.
              </p>
              <p className={Style.slogan3}>
                The Advancement to Future Automobile Components with Virtual
                Twin Technology
              </p>
              <li>
                <p className={Style.info_tit}>부처/과제명</p>
                <ul className={Style.info_list}>
                  <li>
                    <span>부처 : </span>
                    <span>산업통상자원부</span>
                  </li>
                  <li>
                    <span>과제명 : </span>
                    <span>버추얼 기반 미래차 부품 고도화 사업</span>
                  </li>
                </ul>
              </li>
              <li>
                <p className={Style.info_tit}>기간</p>
                <p className={Style.info_txt}>2022.04~2024.12</p>
              </li>
              <li>
                <p className={Style.info_tit}>주관기관 및 참여기관</p>
                <ul className={Style.info_list}>
                  <li>
                    <span>주관기관 : </span>
                    <span>경남테크노파크</span>
                  </li>
                  <li>
                    <span>참여기관 : </span>
                    <span>한국산업기술시험원, 인제대학교</span>
                  </li>
                </ul>
              </li>
              <li>
                <p className={Style.info_tit}>목적</p>
                <p className={Style.info_txt}>
                  경남도 부품사들의 미래차 전환을 위한 프로세스 및 장비 구축
                </p>
              </li>
              <li>
                <p className={Style.info_tit}>최종목표</p>
                <ul className={Style.info_list}>
                  <li>
                    자동차 부품들의 버추얼 모델 개발 및 주행성능평가 기술 지원
                  </li>
                  <li>
                    자동차부품기업의 디지털 전환과 버추얼 개발 프로세스 플랫폼
                    구축
                  </li>
                </ul>
              </li>
            </ul>
            <p className={Style.title}>기대효과</p>
            <div className={Style.bot_info}>
              <dl>
                <dt>
                  <span>기술적 기대효과</span>
                </dt>
                <dd>
                  <ul>
                    <li>
                      실차주행시험을 대체할 수 있는 버추얼 개발 프로세스를
                      도입하여 자동차 부품기업들의{" "}
                      <span>해외시장 경쟁력 제고 가능</span>
                    </li>
                    <li>
                      개발단계(설계, 성능검증)에서 최종 주행성능을 검증하고
                      실차주행 평가를 최소화하여{" "}
                      <span>비용 절감할 수 있음</span>
                    </li>
                    <li>
                      제조기술과 IT기술을 융합 시키고, 부품간 기능 통합으로
                      모듈수준의 생산능력을 갖춘 자동차 부품 중견기업의 출현
                      가능성을 높이며, 이를 바탕으로{" "}
                      <span>수평적 수요공급 구조를 형성하는데 기여</span>
                    </li>
                    <li>
                      향후 ADAS 및 자율차 부품기업 유치와 미래차 부품 기업전환
                      육성을 통해 버추얼 개발 프로세스 기반의{" "}
                      <span>미래자동차 기술단지 조성 가능</span>
                    </li>
                  </ul>
                </dd>
              </dl>
              <dl>
                <dt>
                  <span>경제적 기대효과</span>
                </dt>
                <dd>
                  <ul>
                    <li>
                      사업추진 시 2024년까지 <span>지원기업매출 증진</span> ·
                      수혜기업 (현가, 제동, 조향, 파워 트레인 등 R&H, NVH
                      관여부품) 지원으로 매출증대 예상
                    </li>
                    <li>
                      지역 자동차부품기업의 종목다각화, 글로벌 시장진출을 통한{" "}
                      <span>고용위기 타계</span>
                    </li>
                    <li>
                      사업추진에 따른 일자리 <span>고용증진 효과</span>
                    </li>
                  </ul>
                </dd>
              </dl>
            </div>
          </section>
        </SectionOnScroll>
        <SectionOnScroll>
          <section className={Style.greet_box2}>
            <p className={Style.title}>핵심사업</p>
            <div className={Style.top_info}>
              <dl>
                <dt>
                  <img src="/img/battery/greet_pic1.png" alt="" />
                </dt>
                <dd>
                  <p>
                    <span>센터 구축</span>
                  </p>
                  <ul>
                    <li>신규전용공간 ‘미래자동차 버추얼 센터’ 구축</li>
                    <li>경남도 미래차 전환과 시험평가를 위한 장비 구축</li>
                  </ul>
                </dd>
              </dl>
              <dl>
                <dt>
                  <img src="/img/battery/greet_pic2.png" alt="" />
                </dt>
                <dd>
                  <p>
                    <span>플랫폼 구축</span>
                  </p>
                  <ul>
                    <li>
                      산·학·연 네트워크 구성 및 운영(기술공유세미나, 간담회,
                      워크숍 등)
                    </li>
                    <li>
                      네트워킹 운영으로 신사업진출, 미래자동차 협업체 구성
                    </li>
                    <li>경남도 미래차부품 전환을 위한 플랫폼 개발</li>
                  </ul>
                </dd>
              </dl>
              <dl>
                <dt>
                  <img src="/img/battery/greet_pic3.png" alt="" />
                </dt>
                <dd>
                  <p>
                    <span>기술지원</span>
                  </p>
                  <ul>
                    <li>
                      미래차부품 디지털모델 개발 및 CAE개선 지원 등 버추얼 개발
                      기초 기술지원
                    </li>
                    <li>
                      지역의 현가, 제동, 조향분야 대표기업 선정 디지털모델개발
                      지원
                    </li>
                    <li>기업지원 업체들 간 성과보고회 결과보고 개최</li>
                  </ul>
                </dd>
              </dl>
              <dl>
                <dt>
                  <img src="/img/battery/greet_pic4.png" alt="" />
                </dt>
                <dd>
                  <p>
                    <span>전문인력양성</span>
                  </p>
                  <ul>
                    <li>지원기업의 수요에 기반한 실무형 인력양성 교육 실시</li>
                    <li>
                      버추얼 개발 장비활용 및 미래차 부품 설계 전문 인력양성
                    </li>
                  </ul>
                </dd>
              </dl>
            </div>
            <p className={Style.title}>사업개요</p>
            <div className={Style.bot_info}>
              <div className={Style.info_l_img}>
                <img src="/img/battery/greet_vision.png" alt="" />
              </div>
              <div className={Style.info_r_list}>
                <dl>
                  <dt>
                    <p>01</p>
                    <p>
                      미래차 부품의 기술 고도화 및 다각화를 위한 개발 프로세서
                      도입과 주행성능평가 기업지원 플랫폼 구축
                    </p>
                  </dt>
                  <dd>
                    <ul>
                      <li>
                        현가, 제동, 조향, 파워트레인 부품의 고도화 및 다각화
                      </li>
                      <li>
                        자동차 부품의 작동과 감지 및 제어시스템이 연동되어
                        조종성, 승차감, 주행 안전성과 편의성을 능동적으로
                        추종하는 부품을 대상
                      </li>
                    </ul>
                  </dd>
                </dl>
                <dl>
                  <dt>
                    <p>02</p>
                    <p>
                      개발단계별 기관보유 연구장비와 기술 및 전문인력을 활용하고
                      인력양성과 기업지원을 제공하는 플랫폼 구축
                    </p>
                  </dt>
                  <dd>
                    <ul>
                      <li>
                        모델링(시스템 모델 및 시험평가환경구성 CAE개선 등),
                        시뮬레이션 평가(SIL·HIL) 등 미래차 부품 고도화를 위한
                        혁신기관 간 연계 체계 구축
                      </li>
                    </ul>
                  </dd>
                </dl>
                <dl>
                  <dt>
                    <p>03</p>
                    <p>
                      미래차 부품화를 위한 버추얼 모델 개발과 기능기반 모델을
                      접목한 실차성능예측 및 부품성능설계 장비구축
                    </p>
                  </dt>
                  <dd>
                    <ul>
                      <li>
                        버추얼 모델(시스템 모델)개발과 주행환경구성을 통해 차량
                        주행 성능을 예측하는 부품사양설계 지원장비 확충
                      </li>
                    </ul>
                  </dd>
                </dl>
              </div>
            </div>
          </section>
        </SectionOnScroll>
        <SectionOnScroll>
          <section className={Style.greet_box3}>
            <p className={Style.title}>버추얼 기반 미래차 개발프로세스</p>
            <div className={Style.top_info}>
              <p className={Style.title_s}>완성차 개발시스템 변화</p>
              <ul>
                <li>
                  <p>
                    <span>INITIAL</span>
                  </p>
                  <p>기획</p>
                </li>
                <li>
                  <p>
                    <span>ARCHITECTURE</span>
                  </p>
                  <p>아키텍처 개발</p>
                </li>
                <li>
                  <p>
                    <span>CONCEPT DESIGN</span>
                  </p>
                  <p>디자인</p>
                </li>
                <li>
                  <p>
                    <span>INTEGRATION</span>
                  </p>
                  <p>
                    <span>설계</span>
                    <span>해석</span>
                  </p>
                </li>
                <li>
                  <p>
                    <span>VALIDATION</span>
                  </p>
                  <p>
                    <span>시험</span>
                    <span>파일럿</span>
                  </p>
                </li>
                <li>
                  <p>
                    <span>SOP</span>
                  </p>
                  <p>
                    <span>양산</span>
                    <span>판매</span>
                  </p>
                </li>
              </ul>
            </div>
            <div className={Style.bot_info}>
              <dl>
                <dt>모델</dt>
                <dd>
                  <div className={Style.process_m1}>형상기반모델(Geometry)</div>
                  <span className={Style.arrow}>
                    <img
                      className={Style.arrow_pc}
                      src="/img/battery/arrow.svg"
                      alt="화살표 이미지"
                    />
                    <span className={Style.arrow_mo}>
                      <IoIosArrowDown />
                    </span>
                  </span>
                  <div className={Style.process_m2}>
                    <span>기능기반 모델(Non-Geometry)</span>
                  </div>
                </dd>
              </dl>
              <dl>
                <dt>구성</dt>
                <dd>
                  <div className={Style.process_m1}>FEM, MBO, CFD 모델</div>
                  <span className={Style.arrow}>
                    <img
                      className={Style.arrow_pc}
                      src="/img/battery/arrow.svg"
                      alt="화살표 이미지"
                    />
                    <span className={Style.arrow_mo}>
                      <IoIosArrowDown />
                    </span>
                  </span>
                  <div className={Style.process_m2}>
                    <span> DB기반 모델</span>, 시스템(특성, 제어, 센서)모델
                  </div>
                </dd>
              </dl>
              <dl>
                <dt>성능</dt>
                <dd>
                  <div className={Style.process_m1}>
                    충돌, 내구, NVH, R&H, 공기역학, 열유동
                  </div>
                  <span className={Style.arrow}>
                    <img
                      className={Style.arrow_pc}
                      src="/img/battery/arrow.svg"
                      alt=""
                    />
                    <span className={Style.arrow_mo}>
                      <IoIosArrowDown />
                    </span>
                  </span>
                  <div className={Style.process_m2}>
                    <span>섀시제어, R&H</span>, PT동력, ADAS, 열관리
                  </div>
                </dd>
              </dl>
            </div>
            <div className={Style.process_txt}>
              <p>버추얼 개발 프로세스란?</p>
              <ul>
                <li>
                  최근 완성차사는 미래자동차 시대를 대비하여{" "}
                  <span>저비용, 고성능 차량 개발</span>과 판매를 목적으로
                  데이터를 기반으로{" "}
                  <span>
                    디지털 차량개발 및 운용 프로세스, 버추얼 개발 프로세스 도입
                  </span>
                  과 개선을 추진
                </li>
                <li>
                  버추얼 개발이란 다양한 디지털 데이터를 바탕으로{" "}
                  <span>
                    가상모델 혹은 환경을 구축해 부품을 설계·조립·평가 등
                    자동차를 개발하는 과정
                  </span>
                  을 상당 부분 대체하는 것
                </li>
                <li>
                  설계자가 요구사양대로 DB를 활용하여 신속하게 설계사양을
                  변경·검증할 수 있고,{" "}
                  <span>
                    제작 후 자동차에서 검증하기 힘든 오류 등을 빠르게 확인하고
                    개선
                  </span>
                  해 자동차의 완성도를 높일 수 있는 기술
                </li>
              </ul>
            </div>
          </section>
        </SectionOnScroll>
        <SectionOnScroll>
          <section className={Style.greet_box4}>
            <p className={Style.title}>차량개발 절차</p>
            <div className={Style.cycle_l}>
              <p>
                <span>AS-IS</span>
              </p>
              <img
                className={Style.img_lg}
                src="/img/greet_process1_lg.svg"
                alt=""
              />
              <img
                className={Style.img_sm}
                src="/img/battery/greet_process1_sm.svg"
                alt=""
              />
            </div>
            <div className={Style.cycle_r}>
              <p>
                <span>TO-BE</span>
              </p>
              <img
                className={Style.img_lg}
                src="/img/greet_process2_lg.svg"
                alt=""
              />
              <img
                className={Style.img_sm}
                src="/img/battery/greet_process2_sm.svg"
                alt=""
              />
            </div>
          </section>
        </SectionOnScroll>
        <SectionOnScroll>
          <section className={Style.greet_box5}>
            <p className={Style.title}>진행기관 소개 및 역할</p>
            <div className={Style.top_info}>
              <dl>
                <dt>
                  <img src="/img/battery/greet_pic5.png" alt="" />
                </dt>
                <dd>
                  <div>
                    <p>경남 테크노파크</p>
                    <span> | 미래자동차본부</span>
                  </div>
                  <ul>
                    <li>
                      경남지역 산·학·연·관의 유기적인 협력체계를 구축하여
                      지역혁신기관 간 연계조정 등 지역혁신거점기관으로서
                      지역산업의 기술고도화와 기술집약적 기업의 혁신 성장을
                      촉진하고 지역경제 활성화와 국가경제발전에 기여하는 역할
                      수행
                    </li>
                    <li>
                      디지털 트윈기술 바탕의 미래차 버추얼 부품개발 프로세스
                      기술지원
                    </li>
                    <li>섀시 및 파워트레인 장비활용 기술지원(2016~)</li>
                    <li>미래차부품산업 전환 및 버추얼 개발 기술지원(2022~)</li>
                  </ul>
                </dd>
              </dl>
              <dl>
                <dt>
                  <img src="/img/battery/greet_pic6.png" alt="" />
                </dt>
                <dd>
                  <div>
                    <p>한국산업기술시험원</p>
                    <span> | 기계소재기술센터</span>
                  </div>
                  <ul>
                    <li>
                      KTL은 산업통상자원부 산하 국내 유일 공공 종합시험
                      인증기관, 기업의 기술 성과물 성능시험 및 전 산업분야에
                      대한 시험평가, 측정 장비 교정 등 다양한 서비스 제공
                    </li>
                    <li>
                      미래차 버추얼 부품개발 프로세스 환경 구축 및 기업지원
                    </li>
                    <li>경남도 미래차 버추얼 부품 산업 동향 (2022~)</li>
                    <li>기업지원 및 기술 지도(2022~)</li>
                  </ul>
                </dd>
              </dl>
              <dl>
                <dt>
                  <img src="/img/battery/greet_pic7.png" alt="" />
                </dt>
                <dd>
                  <div>
                    <p>인제대학교</p>
                    <span> | 수송기계부품기술혁신센터</span>
                  </div>
                  <ul>
                    <li>
                      인제대학교 산하 수송기계부품기술혁신센터(TIC)는 자동차 및
                      기계부품 관련 기업체를 대상으로 공동 연구장비 활용,
                      공동연구 수행 및 재직자 교육을 지원
                    </li>
                    <li>
                      미래차 버추얼 부품개발 프로세스 전문인력양성 및 기업지원
                    </li>
                    <li>디지털 모델개발 인력양성 프로그램 운영(2022~ )</li>
                    <li>기업지원 및 교육 지도(2022~)</li>
                  </ul>
                </dd>
              </dl>
            </div>
          </section>
        </SectionOnScroll>
      </div>
    </>
  );
}

```

![[예제 2. 프린트.png]]

