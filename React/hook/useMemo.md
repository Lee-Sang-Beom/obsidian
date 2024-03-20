
#### 1. `useMemo`란?

- 컴포넌트 최적화를 위한 React hook 중 하나이다.
	- **`useMemo`**
	- `useCallback`

- 컴포넌트가 렌더링될 때마다 **동일한 값** 반환하는 함수를 **계속 반복하여 호출하는 것**이 아니라, 

#### 2. `useMemo`의 구조

 - **인자 1**
	 - callback 함수이다.
	 - **memorization**할 값을 계산해 return하는 함수이다.

 - **인자 2**
	 - 의존성배열: 배열 내 값이 업데이트될 때만 콜백함수를 호출해 memorization된 값을 업데이트하는 역할을 한다.

- **사용 예시**
```tsx
// value값이 변할 때마다, calculate() 함수의 결과로 반환되는 값이 newValue에 들어간다.
 const newValue = useMemo(()=> {
    return calculate();
 }, [value])
```
===

 2. 예제 1 (계산기)
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


      <h3>계산기</h3> {/* APP컴토넌트가 함수형 컴포넌트라, EASY한 변경에도, hardSum이 초기화되면서 느려짐*/}
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

===

3. 예제 2 (obj 확인하기)

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