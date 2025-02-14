- React의 생명주기(Lifecycle)는 컴포넌트가 생성, 업데이트, 소멸되는 과정을 다룬다.
	- 컴포넌트가 DOM에 삽입되고, 상태나 props가 변경될 때 또는 제거될 때 React는 특정 메서드를 호출하거나 Hooks를 통해 작업을 처리한다.

---
### 1. 클래스형 컴포넌트의 생명주기

- 클래스형 컴포넌트의 생명주기는 크게 세 단계로 나눌 수 있다.
	1. **마운트(Mount)**: 컴포넌트가 처음 DOM에 삽입될 때.
	2. **업데이트(Update)**: 상태(state)나 속성(props)이 변경될 때.
	3. **언마운트(Unmount)**: 컴포넌트가 DOM에서 제거될 때.

##### ※ 주요 생명주기 메서드
###### 1. 마운트 단계
- 마운트 단계에서는 아래 메서드가  순서대로 호출된다.

1. **`constructor(props)`**
    - 클래스 컴포넌트 초기화될 때 호출된다.
    - 컴포넌트가 생성될 때 가장 먼저 실행되며 주로 초기 `state` 설정하거나 이벤트 핸들러를 바인딩하는 데에 사용된다.
    - `constructor`에서는 DOM 조작이나 API 호출과 같은 부수효과(side effect)를 실행하지 않아야 한다.
```js
constructor(props) { // props : 부모컴포넌트에서 전달된 데이터를 읽을 수 있다.
  super(props); // `super(props)`는 React.Component의 생성자를 호출하며, 반드시 호출해야 `this`를 사용할 수 있습니다.
  this.state = { count: 0 }; // 초기 state 설정
  this.handleClick = this.handleClick.bind(this); // 이벤트 핸들러 바인딩
}
```

2. `static getDerivedStateFromProps(props, state)`
	- **React 클래스 컴포넌트의 정적(static) 생명주기 메서드**로, 컴포넌트가 새로운 `props`를 받거나 상태가 변경될 때 호출된다. 
		- 이 메서드를 사용하면 **`props`에 따라 `state`를 업데이트**할 수 있다.
	
	- 클래스의 정적 메서드이기 때문에 `THIS`에 접근할 수 없으며, `props`, `state`를 매개변수로 받아 작업을 수행할 수 있다.
	- DOM에 영향을 미치기 전에 실행되어 `render()` 이전에 호출된다.
```js
static getDerivedStateFromProps(nextProps, prevState) {
  if (nextProps.initialCount !== prevState.count) {
    return { count: nextProps.initialCount };
  }
  return null;
}
```

3. `ReactDOM.render()`
	- 컴포넌트의 UI를 반환한다.
	- DOM 조작은 불가능하며, 순수 함수처럼 동작해야 한다.
```js
ReactDOM.render(<App />, document.getElementById('root'));
```

4. `componentDidMount()`
	- 컴포넌트가 DOM에 **완전히 삽입된 후** 호출된다.
	- 이 단계에서는 컴포넌트가 화면에 렌더링된 상태이므로, **API 호출**, **타이머 설정**, **DOM 조작**과 같은 작업을 수행하기 적합하다.
		- 서버에서 데이터를 가져와 state를 업데이트할 때 사용가능하다.
		- 주기적인 작업을 위한 타이머 설정 및 브라우저의 이벤트 리스너를 추가 가능하다.
		- 렌더링 후 DOM이 준비되었기 때문에 DOM에 접근 가능하다.
```js
componentDidMount() {
  // API 호출로 데이터 가져오기 
  fetch("https://api.example.com/data") 
  .then((response) => response.json()) 
  .then((data) => this.setState({ data }));
}
```


###### 2. 업데이트 단계
- 컴포넌트의 상태(state)나 속성(props)이 변경될 때 호출된다.

1. **`static getDerivedStateFromProps(props, state)`**
    - 마운트와 업데이트 모두에서 호출되며, `render()` 이전에 호출되어 `props` 가 변경될 때 `state`를 업데이트할 필요가 있는 경우 사용된다.
```js
static getDerivedStateFromProps(nextProps, prevState) {
  if (nextProps.someProp !== prevState.someState) {
    return { someState: nextProps.someProp };
  }
  return null;
}
```

2. **`shouldComponentUpdate(nextProps, nextState)`**
    - 컴포넌트가 리렌더링될지를 결정한다.
	    - 기본적으로 모든 상태변화, `props` 변화에 따라 컴포넌트는 재렌더링된다.
	    - 이 메서드를 사용하여 렌더링 조건을 커스터마이징하고 성능 최적화를 할 수 있다.
	
    - `true`를 반환하면 렌더링, `false`를 반환하면 렌더링하지 않는다.
```js
shouldComponentUpdate(nextProps, nextState) {
  // 상태나 props가 변경되지 않았다면 리렌더링하지 않음
  return nextProps.value !== this.props.value || nextState.count !== this.state.count;
}
```

3. `render()`
	- 새로운 UI를 렌더링한다.
```jsx
class MyComponent extends React.Component {
  render() {
    return <div>{this.props.message}</div>;
  }
}

```

4. **`getSnapshotBeforeUpdate(prevProps, prevState)`**
	- **`render` 이후, DOM이 실제로 업데이트되기 전**에 호출되어, 컴포넌트가 **DOM에 업데이트되기 직전의 상태**를 가져온다.
	- 해당 메서드로 반환된 반환값은 `componentDidUpdate` 메서드에서 세 번째 매개변수로 전달된다.
```js
getSnapshotBeforeUpdate(prevProps, prevState) {
  if (prevProps.scrollPosition !== this.props.scrollPosition) {
    return window.scrollY; // 이전 스크롤 위치 반환
  }
  return null;
}

componentDidUpdate(prevProps, prevState, snapshot) {
  if (snapshot !== null) {
    console.log("이전 스크롤 위치:", snapshot);
  }
}

```

5. `componentDidUpdate(prevProps, prevState, snapShot)`
	- 컴포넌트가 업데이트된 후 호출되며, **렌더링 결과가 DOM에 반영된 후** 부수 효과(side effects)를 처리할 때 사용된다.
	- `props`, `state` 변화 후 DOM 조작, API 호출 등에 적합하다.
		- 주의: 무한 루프를 방지하기 위해 `setState`를 호출할 때 **조건문을 사용**해야 한다.
```js
componentDidUpdate(prevProps, prevState, snapshot) {
  // 이전 props 또는 state와 현재 상태 비교
  if (prevProps.value !== this.props.value) {
    this.fetchData(); // props 변화에 따라 데이터 재요청
  }
}

```

| 메서드                        | 호출 시점                                  | 주요 작업                    |
| -------------------------- | -------------------------------------- | ------------------------ |
| `getDerivedStateFromProps` | `props` 변경 시, `render()` 전에 호출         | 상태 업데이트                  |
| `shouldComponentUpdate`    | 상태 또는 `props`가 변경될 때, `render()` 전에 호출 | 렌더링 여부 결정                |
| `getSnapshotBeforeUpdate`  | DOM 업데이트 전에 호출                         | DOM의 이전 상태 저장            |
| `componentDidUpdate`       | DOM 업데이트 후 호출                          | 부수 효과 처리, 데이터 갱신, DOM 조작 |

###### 3. 언마운트 단계
- 컴포넌트가 DOM에서 제거될 때 호출된다.

1. **`componentWillUnmount()`**
	- 컴포넌트가 DOM에서 제거되기 전에 호출된다.
	- 타이머 해제, 이벤트 리스너 제거 등 리소스를 정리하는 데 사용된다.
```js
componentWillUnmount() {
  console.log("컴포넌트가 언마운트됩니다.");
}
```

---
### 3. 함수형 컴포넌트의 생명주기

- React Hooks를 사용하면 함수형 컴포넌트에서도 생명주기 관리를 할 수 있다.
	- 함수형 컴포넌트에서는 `useEffect`가 핵심 역할을 한다.

##### ※ `useEffect`로 생명주기 관리
```js
useEffect(() => {
  // 실행할 코드
  return () => {
    // 정리(clean-up) 코드
  };
}, [dependencies]);
```
- **의존성 배열**
	- `[]`: 마운트와 언마운트 시 한 번 실행.
	- `[dependencies]`: 특정 값이 변경될 때 실행.
	- 생략 시, 매 렌더링마다 실행.


##### ※ 생명주기 단계별 구현
###### 1. 마운트 단계
```js
useEffect(() => {
  console.log("마운트됨");
  return () => {
    console.log("언마운트됨");
  };
}, []);
```

###### 2. 업데이트 단계
```js
useEffect(() => {
  console.log("업데이트됨");
}, [someState]);
```

###### 3. 언마운트 단계
```js
useEffect(() => {
  const timer = setInterval(() => console.log("타이머 실행 중"), 1000);
  return () => clearInterval(timer); // 정리 코드
}, []);
```

---
#### 4. 클래스와 함수형 컴포넌트 비교
| 단계   | 클래스 컴포넌트             | 함수형 컴포넌트 (React Hooks)        |
|------|----------------------|-------------------------------|
| 마운트  | componentDidMount    | useEffect(() => {}, [])       |
| 업데이트 | componentDidUpdate   | useEffect(() => {}, [deps])   |
| 언마운트 | componentWillUnmount | useEffect(() => () => {}, []) |

---
#### 5. React 생명주기 주의사항

1. **Deprecated 메서드**: React 16.3 이후 `componentWillMount`, `componentWillReceiveProps`, `componentWillUpdate`는 더 이상 사용을 권장하지 않는다.
2. **함수형 컴포넌트 사용 증가**: 최신 React 애플리케이션에서는 Hooks 기반의 함수형 컴포넌트 사용이 권장된다.
3. **최적화**: `shouldComponentUpdate`나 `React.memo`를 사용해 불필요한 렌더링을 방지할 수 있다.