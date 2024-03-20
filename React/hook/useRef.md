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


#### 3. 프로젝트 내 사용경험

- `ref`는 위에서 언급했듯 **DOM**에 접근할 수 있는 인터페이스적 역할을 수행해주기 때문에 각종 `element`를 관리하고 추적하는 데에 용이하다.

- 아래 작업은 `ref`를 `<a>`태그에 부여해 active Link가 무엇인지를 탐색하고, 현재 경로(`pathNm`)와  