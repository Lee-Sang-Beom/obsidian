
https://velog.io/@2ast/React-%EC%84%9C%EB%B2%84-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8React-Server-Component%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0



```tsx

export default function ParentRSC(){
	return (
	<>
		<ChildRCC>
		</ChildRCC>
	</>
	)
}
```

- 서버에서 모든 RSC가 순차적으로 실행되며, 중간에 RCC를 만나면 placeholder로 표시해두고 넘어간다고 했다. 즉, RCC는 실행되지 않기 때문에 RCC 내부에서 반환되는 RSC또한 (서버 컴포넌트임에도 불구하고) 서버에서 실행되지 못한다. 이러한 경우 해당 RSC는 RCC와 동일하게 클라이언트에서 동작하게 된다.
- 하지만 children prop을 통해 RSC를 넘기게 되면, 사실상 공통 부모가 렌더링 되는 시점에 RSC가 실행이 되고, 그 결과값을 children으로 전달할 수 있다.

```tsx
function ParentClientComponent({children}) {
	...
  return <div onChange={...}>{children}</div>;
}

function ChildServerComponent() {
	...
  return <div>server component</div>;
}

function ContainerServerComponent() {
  return <ParentClientComponent>
			<ChildServerComponent/>
	</ParentClientComponent>;
}
```

- 만약, 위와 같이 “**ChildServerComponent**”는 “**ParentClientComponent**”의 자식 컴포넌트이지만, 사실상 “**ContainerServerComponent**”를 공통부모로 갖고있기 때문에, “**ContainerServerComponent**”가 렌더링되는 시점에 “**ChildServerComponent**”도 함께 렌더링되어 그 결과값이 “**ParentClientComponent**”에 넘겨지고 있다.