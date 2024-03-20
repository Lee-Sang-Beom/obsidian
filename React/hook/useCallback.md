
#### 1. `useCallback`이란?

- 컴포넌트 최적화를 위한 React hook 중 하나이다.
	- `useMemo`
	- **`useCallback`**

- 컴포넌트가 렌더링될 때마다 **동일한 값**을 반환하는 함수를 **계속 반복하여 호출하는 것**이 아니라는 점이 핵심이다.
	- 처음으로 값을 계산할 때 해당 값을 메모리에 저장한 다음, 그 값이 필요할 때마다 다시 계산하지 않고 **메모리에서 꺼내서 재사용**할 수 있도록 한다.
	- 의존성 배열에 포함된 값이 **업데이트**될 때, 메모리에 저장된 값이 업데이트된다.
	- `useMemo`과 다르게, `useCallback`은 1번째 인자로 전달한 **콜백 함수 자체를 반환하는 것에 주목할 필요**가 있다.


#### 2. `useCallback`의 구조

- 구조 또한, `useMemo`와 동일하다.

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


#### 2. 예제 1

```jsx

import logo from "./logo.svg";
import "./App.css";
import { useState, useReducer, useEffect, useCallback } from "react";

function App() {
  const [num, setNum] = useState(0);
  const [tog, setTog] = useState(true);
  
  // const someFun = () => {
  //   console.log(num);
  // };

  const someFun = useCallback(()=>{
    console.log(`${num}`);
    return;
  }, [num]); 
  /*
    의존성배열 x 
     - 원래는, state num변경으로 재렌더링되면서, sumeFun의 초기화주소가 계속달라짐

     - 하지만, 맨 처음 app 컴포넌트가 호출될 렌더링을 제외하고는, state변경 렌더링에는 useEffect가 불리지 x
       memorization된 함수를 반환 (덕분에 항상 같은 memorization된 함수의 주소를 가짐)
 
     - 하지만 num이 바뀜에따라 항상 someFun은 memorization된 함수만을 계속 재사용하니, console.log()시, 0만 출력됨
       그래서 num 변경때마다 계속 바꿔줄수있어야함
  */
       
  /*
    someFun이 변경될때마다 실행
    num 값이 변경될때마다 재렌더링 -> App() 호출 -> someFun도 함수 객체를 가진 변수로, 호출됨

    soneFun이 같은 동작을 한다 하더라도, obj이기 때문에 다른 메모리주소를 가지기에 변한 것으로 인식
  */

  useEffect(() => {
    console.log("someFun의 변경");
  }, [someFun]);

  return (
    <div>
      <input
        type="number"
        value={num}
        onChange={(e) => setNum(e.target.value)}
      />

      <br />
      <button onClick={someFun}>call someFunc</button>
      <button onClick={()=> setTog(!tog)}>test</button> {/* toggle버튼 클릭시에는, memorization된 함수를 계속사용함*/}
    </div>
  );
}

export default App;
```


#### 3. 예제 2 (obj 확인하기)

-  `app.js`
```jsx
import logo from "./logo.svg";
import "./App.css";
import { useState, useReducer, useEffect, useCallback } from "react";
import Box from "./Box";

function App() {
  const [size, setSize] = useState(100);
  const [isDark, setIsDark] = useState(false);

  /* 
   - 아래처럼 하면, changeTheme 버튼으로 인한 재 렌더링으로, 
     app()이 재호출되면서 createBoxStyle이 재초기화 됨. 

   - 함수객체가 createBoxStyle이기때문에 메모리주소가 달라져,
     다른값으로 생각하기 때문에 Box.js의 useEffect가 실행됨

   - 이렇게 불필요한 함수의 재렌더링을 막기위해 useCallback사용
  */ 

  // const createBoxStyle = () => {
  //   return {
  //     backgroundColor: "pink",
  //     width: `${size}px`,
  //     height: `${size}px`,
  //   };
  // };

  const createBoxStyle = useCallback(()=>{
    return {
          backgroundColor: "pink",
          width: `${size}px`,
          height: `${size}px`,
        };
  },[size])

  return (
    <div style={{
      backgroundColor : isDark ? "black" : "yellow",
    }}>
      <input
        type="number"
        value={size}
        onChange={(e) => setSize(e.target.value)}
      />
      <button onClick={()=>setIsDark(!isDark)}>change Theme</button>
      <Box createBoxStyle={createBoxStyle} />
    </div>
  );
}

export default App;
```

- `box.js`
```jsx
import { useEffect, useState } from "react";

export default function Box({createBoxStyle}){

    const [style, setStyle] = useState({});
    
    useEffect(()=>{
        console.log('box size 조절');
        setStyle(createBoxStyle());
    },[createBoxStyle]);

    return(
        <div style={style}>
        </div>
    );
}
```
