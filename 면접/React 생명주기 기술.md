
- React 생명주기 단계는 컴포넌트가 DOM에 처음 삽입되는 마운트, State나 Props가 변경될 때의 업데이트, 컴포넌트가 DOM에서 제거될 때의 언마운트로 구분할 수 있습니다.
- 클래스형 컴포넌트에서 생명주기 별 핵심 함수는 componentDidMount, componentDidUpdate, componentWillUnmount 등이 있고, 함수형 컴포넌트에서는 useEffect의 이펙트 함수, 의존성 배열, 클린업 함수 등을 이용하여 관리할 수 있습니다.