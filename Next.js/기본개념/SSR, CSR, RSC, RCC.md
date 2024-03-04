
https://velog.io/@2ast/React-%EC%84%9C%EB%B2%84-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8React-Server-Component%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0


##### RCC는 RSC를 직접 return해줄 수 없으며, 반드시 children prop의 형태로 넘겨주어야 한다.
```tsx
const ChildServerComponent = () =>{
	...
  return <div>server component</div>;
}

const ParentClientComponent = () =>{
	...
	return <div><ChildServerComponent/></div>;
}

function ContainerServerComponent() {
  return <ParentClientComponent/>;
}
```

- 서버에서 모든 RSC가 순차적으로 실행되며, 중간에 RCC를 만나면 placeholder로 표시해두고 넘어간다고 했다. 즉, RCC는 실행되지 않기 때문에 RCC 내부에서 반환되는 RSC또한 (서버 컴포넌트임에도 불구하고) 서버에서 실행되지 못한다. 이러한 경우 해당 RSC는 RCC와 동일하게 클라이언트에서 동작하게 된다.
- 하지만 children prop을 통해 RSC를 넘기게 되면, 사실상 공통 부모가 렌더링 되는 시점에 RSC가 실행이 되고, 그 결과값을 children으로 전달할 수 있다.

```tsx

function ChildServerComponent() {
	...
  return <div>server component</div>;
}

function ParentClientComponent({children}) {
	...
  return <div onChange={...}>{children}</div>;
}

function ContainerServerComponent() {
  return <ParentClientComponent>
			<ChildServerComponent/>
	</ParentClientComponent>;
}
```

- 만약, 위와 같이 “**ChildServerComponent**”는 “**ParentClientComponent**”의 자식 컴포넌트이지만, 사실상 “**ContainerServerComponent**”를 공통부모로 갖고있기 때문에, “**ContainerServerComponent**”가 렌더링되는 시점에 “**ChildServerComponent**”도 함께 렌더링되어 그 결과값이 “**ParentClientComponent**”에 넘겨지고 있다.



#### 2. ssr과 rsc
- 전통적인 SSR: 서버에서 컴포넌트 해석하여, 매 페이지마다 서버에서 html 생성해서 받음
- 전통적인 CSR: 빈 HTML, js 번들받고 클라이언트에서 컴포넌트 렌더링
- nextjs SSR: 초기로딩시, 서버에서 html파일을 ssr방식으로 받아오고, js번들도 같이받아와(csr) 페이지 상호작용을 천천히 그려나감

- rsc1: 서버에서 스트림형식 같은 직렬화과정을 거치기 때문에 번들필요없고, RSC에서 사용하는 외부라이브러리도 번들에 포함안되기때문에 제로번들
- rsc2: 기존 getServerSideprops같은 걸로 서버에 접근했었는데, rsc자체가 서버에서 수행되므로(직렬화를 서버가해줌) 저게 필요없음
- rsc3: rsc는 최종결과물이 html이 아니라 직렬화된 스트림형식으로 데이터를 받으므로, 클라이언트에서는 스트림을 해석해 리액트 가상돔을 형성하고, 돔에서 변경된 부분만을 바꿔치기하는 재조정(reconciliation)과정을 거치기 때문에, 화면에 변경사항이 있어서 새 정보를 받아와야 하더라도, 새로운 스크린 자체를 갈아끼우는게 아니라, 기존 화면의 state 등의 context를 유지한 채로 변경사항만 선택적으로 변경할 수 있음