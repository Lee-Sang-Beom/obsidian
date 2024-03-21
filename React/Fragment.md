
#### 1. 사용배경

- React에서는 하나의 컴포넌트가 여러 개의 `element`를 반환한다. 

- 하지만, React 컴포넌트를 return하는 부분에서 사용하는 `jsx`, `tsx`문법을 사용할 때는 최상위 태그가 반드시 1개로만 구성되어 있어야 한다.
	- React는 하나의 컴포넌트만을 return할 수 있기 때문이다.


#### 2. 사용 방법

- 기본적으로는 `<React.Fragment></React.Fragment>` 혹은 `<></>`로 사용할 수 있다.

- 대표적으로 아래와 같은 table이 React Fragment의 사용 예시로 등장한다.