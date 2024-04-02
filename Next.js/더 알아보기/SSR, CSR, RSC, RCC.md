
#### 1. Next.js 의 RSC, RCC, SSR, CSR 알아보기

 > [2ast님의 포스트](https://velog.io/@2ast/React-%EC%84%9C%EB%B2%84-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8React-Server-Component%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)

- 위의 **포스트**에서는, Next.js의 RSC(React Server Component), RCC(React Client Component)의 동작과 이것들이 Next.js의 SSR과 어떻게 맞물리는지를 굉장히 알기 쉽게 설명하고 있다.

- 여기서는, 링크한 포스트에서 주의깊게 살펴보아야 할 내용을 요약하고, 잘 이해되지 않았던 내용을 다시금 정리하며 Next.js의 컴포넌트 동작에 대해 좀 더 자세히 알아가보고자 한다.

#### 2. RSC(React Server Component) vs RCC(React Client Component)

- 
#### n. 왜 함수는 직렬화하지 못할까?

함수는 일반적으로 직렬화할 수 없습니다. 직렬화는 데이터나 객체를 바이트 스트림 또는 문자열로 변환하는 과정을 말합니다. 이것은 데이터를 저장하거나 전송하기 쉽게 만들어줍니다.

함수는 실행 가능한 코드를 담고 있기 때문에 일반적으로 직렬화할 수 없습니다. 함수는 프로그램이나 스크립트의 일부로서 동작하며, 자바스크립트 엔진에게 실행되어야만 유용한 역할을 합니다. 함수를 직렬화하려면 그 내용뿐만 아니라 실행 컨텍스트와 함께 모든 종속성을 저장해야 하는데, 이는 일반적으로 복잡하고 비효율적입니다.

대신, 함수의 동작 또는 로직을 나타내는 데이터를 직렬화하고, 이 데이터를 이용하여 새로운 함수를 생성하는 방식을 사용할 수 있습니다. 이것이 일반적인 패턴으로, 예를 들어 JSON 형식으로 함수의 설정이나 구성을 저장하고, 이를 읽어와서 해당 함수를 생성하는 것입니다.

함수를 직렬화할 수 없다는 것은 일반적으로 프로그래밍 언어 및 환경에 대한 제약사항입니다. 몇몇 특정한 상황에서는 함수를 직렬화할 수 있도록 하는 라이브러리나 도구가 있을 수 있지만, 이는 일반적인 상황에서는 드물고, 일반적으로 권장되지 않습니다.

---

함수는 프로그램에서 작업을 수행하는 코드 조각이에요. 하지만 이 코드 조각을 그대로 저장하거나 전송하는 것은 어려워요. 왜냐하면 함수는 단순한 데이터가 아니라 실행되어야 하는 코드이기 때문이에요.

그래서 대신, 함수가 하는 일에 대한 설명이나 함수의 설정 정보를 저장할 수는 있어요. 이 정보를 이용해 나중에 같은 작업을 하는 새로운 함수를 만들 수 있어요. 예를 들어, 함수가 받는 입력과 그에 따른 출력에 대한 정보를 저장하고, 이를 사용하여 나중에 같은 입력에 대해 같은 결과를 만들어내는 함수를 만들 수 있어요.

함수를 직렬화한다는 것은 그 함수를 완전히 저장하고 나중에 불러와 실행할 수 있다는 것을 의미하는데, 이것은 일반적으로 어렵고 복잡한 과정입니다. 함수 대신 함수의 동작에 관한 정보를 저장하고 나중에 함수를 새로 만드는 것이 더 간단하고 일반적인 방법입니다.


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