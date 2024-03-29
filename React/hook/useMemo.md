
#### 1. `useMemo`란?

- 컴포넌트 최적화를 위한 React hook 중 하나이다.
	- **`useMemo`**
	- `useCallback`

- 컴포넌트가 렌더링될 때마다 **동일한 값**을 반환하는 함수를 **계속 반복하여 호출하는 것**이 아니라는 점이 핵심이다.
	- 처음으로 값을 계산할 때 해당 값을 메모리에 저장한 다음, 그 값이 필요할 때마다 다시 계산하지 않고 **메모리에서 꺼내서 재사용**할 수 있도록 한다.
	- 의존성 배열에 포함된 값이 **업데이트**될 때, 메모리에 저장된 값이 업데이트된다.
	- `useCallback`과 다르게, `useMemo`는 **함수의 결과를 반환하는 것에 주목할 필요**가 있다.


#### 2. `useMemo`의 구조

 - **인자 1**
	 - **callback 함수**이다.
	 - **memorization**할 값을 계산해 return하는 함수이다.

 - **인자 2**
	 - 의존성배열(dependancyArray): 배열 내 값이 업데이트될 때만 콜백함수를 호출해 memorization된 값을 업데이트하는 역할을 한다.

- **사용 예시**
```tsx
// value값이 변할 때마다, calculate() 함수의 결과로 반환되는 값이 newValue에 들어간다.
 const newValue = useMemo(()=> {
    return calculate();
 }, [value])
```


#### 3. 예제 1 (계산기)

```jsx
import logo from "./logo.svg";
import "./App.css";
import { useState, useRef, useEffect, useMemo } from "react";

const hardCal = (number) => {
  console.log("어려운!");
  for (let i = 0; i < 999999999; i++) {}
  return number + 10000;
};

const easyCal = (number) => {
  console.log("쉬운!");
  return number + 1;
};

function App() {
  const [hardNum, setHardNum] = useState(1);
  // const hardSum = hardCal(hardNum);
  const hardSum = useMemo(()=>{
    return hardCal(hardNum); // memorization할 함수
  }, [hardNum]); // hardNum값이 바뀔때만 hardCal을 호출하여, memorization으로 리턴하는 값 업데이트

  const [easyNum, setEasyNum] = useState(1);
  const easySum = easyCal(easyNum);

  return (
    <div>
      <h3>계산기</h3>
      <input
        type="number"
        value={hardNum}
        onChange={(e) => setHardNum(parseInt(e.target.value))}
      />
      <span> + 10000 = {hardSum}</span>


      <h3>계산기</h3> 
      {/* APP컴토넌트가 함수형 컴포넌트라, EASY한 변경에도, hardSum이 초기화되면서 느려짐*/}
      <input
        type="number"
        value={easyNum}
        onChange={(e) => setEasyNum(parseInt(e.target.value))}
      />
      <span> + 1 = {easySum}</span>
    </div>
  );
}

export default App;
```


#### 4. 예제 2 (Object 확인하기)

```jsx

import logo from "./logo.svg";
import "./App.css";
import { useState, useRef, useEffect, useMemo } from "react";

function App() {
  const [num, setNum] = useState(0);
  const [isKorea, setIsKorea] = useState(true);
  // const location = {
  //   country: isKorea ? "한국" : "외국",
  // };

  const location = useMemo(() => {
    return { 
      country: isKorea ? "한국" : "외국" 
    };
  }, [isKorea]);

  /* 
    - location이 obj이면, num만 변경해도 useEffect가 호출됨
    - 원시타입은 값이 변수라는 상자에 바로 들어가는 모습이라면
    - 객체(obj)타입은 객체가 크기때문에 바로 변수라는 상자에 할당되는게 아니라, 
      메모리 상 공간이 할당되고, 그 메모리 안에 보관됨. 

      변수 안에는 그 객체가 담긴 메모리의 주소가 당김.

      그렇기에 만약 아래와 같은 obj 변수가 있을 때 내용물은 같지만, 변수 안에는 실제 메모리주소가 있고,
      그 변수를 비교할때, 변수 내에 있는 요소인 메모리주소가 달라 loc1 !== loc2이다.

        const location1 = {
          country: isKorea ? "한국" : "외국"
        };

        const location2 = {
          country: isKorea ? "한국" : "외국"
        };

    - 해당 예제에서, app컴포넌트도 함수형 컴포넌트이기 때문에 num값을 변경시키면 app()도 다시 불려오게되고, 
    location obj는 다른 메모리주소를 갖는 다른 obj가 되기 때문에 useEffect가 불리게 됨

      const location = useMemo(() => {
        return { 
          country: isKorea ? "한국" : "외국" 
        };
      }, [isKorea]);

    - 그래서, useMemo로 isKorea가 변경될때만 return하는 방식으로 location을 원하는 때에 변경시켜주어 useEffect를 부르는 방법을 사용 가능하다
  */
  
  useEffect(() => {
    console.log("useEffect 호출");
  }, [location]);

  return (
    <div>
      <h2>몇 끼 먹어요?</h2>
      <input
        type="number"
        value={num}
        onChange={(e) => setNum(e.target.value)}
      />

      <br />
      <h2>어느 나라에 있어요?</h2>
      <p>나라 : {location.country}</p>
      <button onClick={() => setIsKorea(!isKorea)}>비행기 타자</button>
    </div>
  );
}

export default App;
```


#### 5. 프로젝트 내 사용경험 (TableHeader)

- 값을 메모리제이션하는`useMemo` hook은 사용자의 이벤트에 의해 자주 바뀌지 않는 요소에 사용하면 좋을 것으로 판단했다.
	- 자주 바뀌는 요소의 대표적 예시는 Form 요소(input, checkbox 등)

- 그래서, 값을 표시만 하는 **테이블 요소**에 적합하다고 판단하여 작성한 경험이 있다.
	- 같은 페이지에 많은 Form 요소가 있으며 해당 요소가 상태로서 관리되어 컴포넌트에 재렌더링이 일어나도, 서버에서 받아온 `data`가 변하지 않는 이상 값을 반환하는 콜백함수는 다시 실행되지 않는다.

```tsx
  // ...

  // STEP1
  const step1InputListHeader: TableHeader[] = useMemo(() => {
    return [
      {
        name: "사업장명",
        value: "companyNm",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.companyNm || ""}</p>;
        },
        width: "30%",
      },
      {
        name: "사업자등록번호",
        value: "companyBizNo",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return (
            <p>
              {tr.companyBizNo
                ? tr.companyBizNo.replace(/(\d{3})(\d{2})(\d{5})/, "$1-$2-$3")
                : ""}
            </p>
          );
        },
      },
      {
        name: "업종",
        value: "companyTpbizNm",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.companyTpbizNm ? tr.companyTpbizNm : ""}</p>;
        },
      },
      {
        name: "대표자",
        value: "companyRprsvNm",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.companyRprsvNm ? tr.companyRprsvNm : ""}</p>;
        },
      },
      {
        name: "주소",
        value: "companyRoadNmAddr",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.companyRoadNmAddr ? tr.companyRoadNmAddr : ""}</p>;
        },
      },
      {
        name: "연락처",
        value: "companyTelNo",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return (
            <p>
              {tr.companyTelNo
                ? tr.companyTelNo.replace(
                    /(\d{2,3})(\d{3,4})(\d{4})/,
                    "$1-$2-$3"
                  )
                : ""}
            </p>
          );
        },
      },
      {
        name: "면적",
        value: "companyLdar",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return (
            <p>
              {tr.companyLdar !== null && tr.companyLdar !== undefined
                ? `${tr.companyLdar}㎡`
                : ""}
            </p>
          );
        },
      },
    ];
  }, [data]);

  // STEP2
  const step2InputListHeader: TableHeader[] = useMemo(() => {
    return [
      {
        name: "화학물질명(국문)",
        value: "mttrnmkor",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.mttrnmkor || ""}</p>;
        },
        width: "30%",
      },
      {
        name: "화학물질명(영문)",
        value: "mttrnmeng",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.mttrnmeng ? tr.mttrnmeng : ""}</p>;
        },
      },
      {
        name: "CAS 번호",
        value: "casNo",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.casNo ? tr.casNo : ""}</p>;
        },
      },
      {
        name: "성상",
        value: "i0202",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.i0202 ? tr.i0202 : ""}</p>;
        },
      },
      {
        name: "색상",
        value: "i0204",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.i0204 ? tr.i0204 : ""}</p>;
        },
      },
      {
        name: "냄새",
        value: "i04",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.i04 ? tr.i04 : ""}</p>;
        },
      },
      {
        name: "인화점",
        value: "i14",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.i14 ? tr.i14 : ""}</p>;
        },
      },
      {
        name: "밀도(kg/m3)",
        value: "i26",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.i26 ? tr.i26 : ""}</p>;
        },
      },
      {
        name: "점도(kg/m-s)",
        value: "i36",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.i36 ? tr.i36 : ""}</p>;
        },
      },
      {
        name: "자연발화온도",
        value: "i32",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.i32 ? tr.i32 : ""}</p>;
        },
      },
      {
        name: "저장탱크용량",
        value: "strgTnkSz",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.strgTnkSz ? tr.strgTnkSz : ""}</p>;
        },
      },
      {
        name: "누출량",
        value: "lkgSz",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.lkgSz ? tr.lkgSz : ""}</p>;
        },
      },
      {
        name: "누출시간(초)",
        value: "lkgTime",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.lkgTime !== null ? tr.lkgTime.toString() : ""}</p>;
        },
      },
      {
        name: "분자량",
        value: "mass",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.mass !== null ? tr.mass.toString() : ""}</p>;
        },
      },
      {
        name: "pac2(ppm)",
        value: "pac2",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.pac2 !== null ? tr.pac2.toString() : ""}</p>;
        },
      },
    ];
  }, [data]);

  // STEP3
  const step3InputListHeader: TableHeader[] = useMemo(() => {
    return [
      {
        name: "기준일시",
        value: "mttrnmkor",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return (
            <p>
              {tr.stdDt
                ? moment(tr.stdDt, "YYYYMMDDHHmm").format("YYYY-MM-DD HH:mm")
                : ""}
            </p>
          );
        },
        width: "30%",
      },
      {
        name: "기온(℃)",
        value: "tmpr",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.tmpr ? tr.tmpr : ""}</p>;
        },
      },
      {
        name: "습도(%)",
        value: "hmdt",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.hmdt ? tr.hmdt : ""}</p>;
        },
      },
      {
        name: "풍속(m/s)",
        value: "wsd",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.wsd ? tr.wsd : ""}</p>;
        },
      },
      {
        name: "1시간 후 풍속(m/s)",
        value: "wsd1h",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.wsd1h ? tr.wsd1h : ""}</p>;
        },
      },
      {
        name: "4시간 후 풍속(m/s)",
        value: "wsd4h",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.wsd4h ? tr.wsd4h : ""}</p>;
        },
      },
      {
        name: "8시간 후 풍속(m/s)",
        value: "wsd8h",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.wsd8h ? tr.wsd8h : ""}</p>;
        },
      },
      {
        name: "풍향",
        value: "vec",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.vec ? tr.vec : ""}</p>;
        },
      },
      {
        name: "대기안정도",
        value: "atmsStblPct",
        accessFn: (tr: DmgeEfctStdResponse, index: number) => {
          return <p>{tr.atmsStblPct ? tr.atmsStblPct : ""}</p>;
        },
      },
    ];
  }, [data]);

  // ...

return (
	{/* ... */}
	<div className={styles.report_wrap}>
	  <div className={styles.report_group_left}>
		{/* STEP1 */}
		<div className={styles.box_group}>
		  <p className={styles.sub_title}>1. 사업장 일반정보</p>
		  <Table<DmgeEfctStdResponse>
			tableCaption="사업장 정보 리스트"
			data={[data.dmgeEfctStdResponse]}
			headers={step1InputListHeader}
			tableType={"horizontal"}
			itemTitle={""}
			ref={null}
		  />
		</div>
	
		{/* STEP2 */}
		<div className={styles.box_group}>
		  <p className={styles.sub_title}>2. 화학물질 정보</p>
		  <Table<DmgeEfctStdResponse>
			tableCaption="화학물질 상세정보 리스트"
			data={[data.dmgeEfctStdResponse]}
			headers={step2InputListHeader}
			tableType={"horizontal"}
			itemTitle={""}
			ref={null}
		  />
		</div>
	
		{/* STEP3 */}
		<div className={styles.box_group}>
		  <p className={styles.sub_title}>3. 피해영향범위 기상 입력정보</p>
		  <Table<DmgeEfctStdResponse>
			tableCaption="화학물질 상세정보 리스트"
			data={[data.dmgeEfctStdResponse]}
			headers={step3InputListHeader}
			tableType={"horizontal"}
			itemTitle={""}
			ref={null}
		  />
		</div>
	  </div>
	</div>
{/* ... */}
)

```