
#### 1. React에서의 context

- **context**는 앱 내에서 전역적으로 사용되는 데이터를 컴포넌트끼리 쉽게 공유할 수 있는 방법을 제공하는 기능이다.
	- 주로, 전역적(global)으로 관리되어야하는 값을 다룰 때 사용한다.

- context가 없는 경우, 부모 자식 간 컴포넌트에서 값을 공유하기 위해서는 **props drilling**을 사용해야 한다. 
	- **props drilling**은 `props`를 통해 부모 컴포넌트에서 여러 중간 자식 컴포넌트를 거쳐, 원하는 특정 컴포넌트까지 데이터를 전달하는 **흐름적인 과정**이다.
	```tsx
	function App(){
		return <Parent name={'myName'}/>
	}
	
	function Parent({name}: {name:string}){
		return <Child name={name}/>
	}
	
	function Child({name}: {name:string}){
		return <p>{name}</p>
	}
	```
    
	- **props drilling**이 많이 발생할수록, 컴포넌트들이 받는 props가 많아지며, 코드가 더러워진다.
    - 또한, 중간에 데이터가 의도치 않게 바뀌게되면 유지보수도 어려워진다.

- `props` 대신 context를 사용해 데이터를 공유하면, 하나의 데이터를 모든 자식컴포넌트들이 `useContext hook`으로 받아오고 사용할 수 있다.

- context는 **꼭 필요할 때만** 사용해야 한다.
	- context를 사용하면 컴포넌트 자체를 재사용하기 어려워질 수 있다.
	- 단지 props drilling을 피하기 위한 목적이면, *component composition*(컴포넌트 합성)을 하는 것이 낫다.
		- [합성](https://happysisyphe.tistory.com/62): **컴포넌트 안에 다른 컴포넌트를 담는 방법**
		- 참고로, **상속**이라는 것도 있는데, `reactjs.org`에서는 [상속 권장사례를 아직 찾지 못했다](https://ko.legacy.reactjs.org/docs/composition-vs-inheritance.html)고 언급하고 있다.


#### 2. 예제 1 (Props drilling)

- `app.js`
```jsx
import logo from './logo.svg';
import './App.css';
import {useState, useRef, useEffect} from "react";
import Page from './component/Page';

function App() {
  const [isDark, setIsDark] = useState(false);
  return (
    <div>
        <Page isDark={isDark} setIsDark={setIsDark}/>
    </div>
  );
}

export default App;
```

- `page.jsx`
```jsx
import React, { useContext } from "react";
import Content from "./Content";
import Footer from "./Footer";
import Header from "./Header";

function Page({isDark, setIsDark}) {

  return (
    <div>
      <div className="page">
        <Header isDark={isDark} />
        <Content isDark={isDark} />
        <Footer isDark={isDark} setIsDark = {setIsDark} />
      </div>
    </div>
  );
}

export default Page;
```

- `content.jsx`
```jsx
import React, { useContext } from "react";
import { ThemeContext } from "../context/ThemeContext";

function Content({isDark}) {
  return (
    <div
      className="content"
      style={{
        backgroundColor: isDark ? "black" : "white",
        color: isDark ? "white" : "black",
      }}
    >
      <h1>Welcome 홍길동!</h1>
    </div>
  );
}

export default Content;
```

- `footer.jsx`
```jsx
import React, { useContext } from "react";
import { ThemeContext } from "../context/ThemeContext";

function Footer({isDark, setIsDark}) {

  const toggle = () => {
    setIsDark(!isDark);
  };

  return (
    <footer
      className="footer"
      style={{
        backgroundColor: isDark ? "black" : "lightgray",
      }}
    >
      <button className="button" onClick={toggle}>
        Dark Mode
      </button>
    </footer>
  );
}

export default Footer;
```

- `header.jsx`
```jsx
import React from "react";
import { useContext } from "react";
import { ThemeContext } from "../context/ThemeContext";
import { UserContext } from "../context/UserContext";

function Header({isDark}) {

  return (
    <header
      className="header"
      style={{
        backgroundColor: isDark ? "black" : "lightgray",
        color: isDark ? "white" : "black",
      }}
    >
      <h1>{`Welcome`}</h1>
    </header>
  );
}

export default Header;
```


#### 3. 예제 2 (context 사용하기)

- 먼저, `useContext hook`을 사용하기 위해서는, context를 관리할 컴포넌트 파일을 만들어야 한다.
	- `createContext()`는 root에서 `Provider`로 감싸면서 원하는 value를 전달하고자 할 때 사용한다.
	- 파라미터로 전달하는 값은 value가 지정되지 않았을 때의 초기값을 의미한다.
```js
import { createContext } from "react";
export const ThemeContext = createContext(null);
```

- `App.js (react에서의 root)`
	- **root** 위치에서, context 관리 파일을 `import`하고, 부모 컴포넌트를 `<ComponentName.Provider>`로 묶어주어야 한다.
	- 그리고, `value` attribute로 접근 가능한 상태값을 넣어주면, `<Provider />` 하위 컴포넌트는 해당 상태값들에 접근할 수 있게 된다.
```jsx
import logo from './logo.svg';
import './App.css';
import {useState} from "react";
import Page from './component/Page';
import {ThemeContext} from './ThemeContext';

function App() {
  // react 예제는 App()이 root이다.
  const [isDark, setIsDark] = useState(false);
  return (
	{/* isDark, setIsDark라는 state, Dispatch를 전달 */}
    <ThemeContext.Provider value={{isDark, setIsDark}}>
        <Page/>
    </ThemeContext.Provider>
  );
}

export default App;
```