
#### 1. Next.js 의 RSC, RCC, SSR, CSR 알아보기

 > [2ast님의 포스트](https://velog.io/@2ast/React-%EC%84%9C%EB%B2%84-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8React-Server-Component%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)

- 위의 **포스트**에서는, Next.js의 RSC(React Server Component), RCC(React Client Component)의 동작과 이것들이 Next.js의 SSR과 어떻게 맞물리는지를 굉장히 알기 쉽게 설명하고 있다.

- 여기서는, 링크한 포스트에서 주의깊게 살펴보아야 할 내용을 요약하고, 잘 이해되지 않았던 내용을 다시금 정리하며 Next.js의 컴포넌트 동작에 대해 좀 더 자세히 알아가보고자 한다.


#### 2. RSC(React Server Component) vs RCC(React Client Component)

- RSC(React Server Component)란, 서버에서 렌더링되는 컴포넌트를 의미한다. RSC에서는, `async`, `await`을 사용하여 비동기 데이터를 가져와 렌더링하는 것이 가능하다.
	- 해당 개념이 등장하기 이전의 **Next.js**에서는, 서버에 접근해 데이터를 **Fetch**한 후 하위 컴포넌트에 데이터를 넘겨주기 위해서는 페이지 최상단에서 `getServerSidePros`함수를 사용하여 일일히 넘겨줘야 했다.
	- 하지만, **서버에서 동작하는 컴포넌트**라면? 서버에서 Fetch를 수행하기 때문에 굳이 `getServerSideProps`를 사용하지 않아도 되게 된다.

- 반면, RCC(React Client Component)는 클라이언트 측에서 렌더링되는 컴포넌트이며, Next.js v12까지 개발 시 사용했던 모든 컴포넌트이기도 하다. 아래의 동작을 관리할 수 있다.
	- **브라우저 API** 및 브라우저 전용 API에 의존하는 사용자 지정 `hook` 사용
	- `react hook` 과 관련된 상태(state) 및 수명 주기 효과 사용
	- 사용자 상호관리 및 Event listener의 관리

![[서버 컴포넌트와 클라이언트 컴포넌트.png]]


#### 3. RSC(React Server Component)의 동작 방식

- 그렇다면, RSC는 어떻게 렌더링 될까?
	- 사용자가 특정 웹사이트에 요청을 보내면, 서버는 **컴포넌트 트리를 `root`부터 직렬화된 JSON형태로 재구성하기 시작한다.**

##### 3-1. 직렬화

- 여기서 **직렬화**란, 데이터 스토리지 문맥에서 데이터 구조나 오브젝트 상태를 동일하거나 **다른 컴퓨터 환경에 저장**하고 **나중에 재구성할 수 있는 포맷으로 변환**하는 과정이다.
	- 즉, 특정 개체를 다른 컴퓨터 환경으로 전송 및 재구성할 수 있는 형태로 변경하는 과정이라고 정리할 수 있다. 
	- `JSON.stringify()`가 직렬화, `JSON.parse`가 역직렬화를 수행하는 함수이다.

- 직렬화 과정은 모든 서버 컴포넌트를 실행한 다음, 컴포넌트 트리가 **JSON 객체 형태의 트리로 재구성될 때까지 진행**된다.

- 이 때, 함수는 직렬화하지 못하는 객체로 분류된다. 이는 함수가 **실행코드 및 실행 컨텍스트를 모두 포함**하는 개념이기 때문이다.
	- 함수는 단순한 데이터가 아니라, 프로그램이나 스크립트의 일부로서 동작하며, 자바스크립트 엔진에게 **실행되어야 하는 코드**이다.
		- 함수가 하는 일에 대한 **설명**이나 함수의 **설정 정보를 저장**할 수는 있으나, 그 함수를 완전히 저장하고 나중에 불러와 실행할 수 있다는 것은 일반적으로 어렵고 복잡한 과정이다.
		
	- 왜냐하면, 함수를 직렬화하려면 자신이 선언된 스코프에 대한 참조를 유지하고, 해당 시점의 외부 변수에 대한 참조를 기억하여야 하기 때문이다. 
		- 이는 함수의 직렬화를 진행하려면, 함수의 **내용** 뿐 아니라 **실행 컨텍스트**와 함께 **스코프, 클로저와 같은 모든 종속성**을 저장해야 한다는 의미이다. 일반적으로 **굉장히 복잡하고, 비효율적**인 일이라고 한다.
		- 때문에, 함수를 직렬화할 수 없다는 것은 일반적으로 프로그래밍 언어 및 환경에 대한 제약사항으로 분류된다. 몇몇 특정한 상황에서는 함수를 직렬화할 수 있도록 하는 라이브러리나 도구가 있을 수 있지만, 이는 일반적인 상황에서는 드물고, 일반적으로 권장되지 않는다고 한다.

##### 3-2. 클라이언트 컴포넌트

- 

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