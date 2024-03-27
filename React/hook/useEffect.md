
#### 1. useEffect란?

- `useEffect hook` 은 컴포넌트의 `부수동작(effect)` 을 관리하는 `hook`으로 기본 `hook` 중에서도 가장 중요한 `hook`이라 할 수 있다.
	- **부수동작**이란 `컴포넌트가 처음 마운트될 때`,`컴포넌트가 업데이트될 때`, `컴포넌트가 언마운트될 때` 일어나는 모든 동작을 의미한다.
	- 작성에 필요한 참고자료의 출처는 [여기](https://c17an.netlify.app/blog/React/useEffect-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0/article/)이다

- 즉, 쉽게 말해 `useEffect hook`이 포함된 컴포넌트가 처음 마운트되거나, 컴포넌트가 리렌더링 될 때, 또는 선언된 변수의 값 등이 변경될 때 실행할 구문들을 정의해놓은 함수`(hook)`이다.


#### 2. 사용 방법

- `useEffect`의 사용 형태는 아래와 같다.
```tsx
useEffect(() => { 
 `실행할 로직` 
}, [`의존성 배열`]);
```

 - **인자 1**
	 - **callback 함수**이다.
	 - **의존성 배열에 선언된 값이 바뀔 때마다** 숳 return하는 함수이다.

 - **인자 2**
	 - 의존성배열(dependancyArray): 배열 내 값이 업데이트될 때만 콜백함수를 호출해 memorization된 값을 업데이트하는 역할을 한다.

- **사용 예시**
```tsx
// value값이 변할 때마다, calculate() 함수의 결과로 반환되는 값이 newValue에 들어간다.
 const newValue = useMemo(()=> {
    return calculate();
 }, [value])
```


```js
import React, {useEffect} from 'react'

  useEffect(
    (이펙트 함수)
    return {
      (클린업 함수)
    }
  }, [의존값]);

  // 이펙트 함수 호출시점 : 컴포넌트가 처음 마운트될 때, 의존값으로 주어진 값이 변경될 때
  // 클린업 함수 호출시점 : 이펙트 함수가 호출되기 전, 컴포넌트가 언마운트될때 각각 클린업이 호출된다.

  // 의존값 배열이 비어 있으면 최초 마운트 시에만 이펙트를 호출한다.
  // 의존값이 존재하지 않으면 컴포넌트가 렌더링될 때마다 이펙트를 호출한다.
```