
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
	- [이미지 출처 - 2ast님의 포스트](https://velog.io/@2ast/React-%EC%84%9C%EB%B2%84-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8React-Server-Component%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)
![[직렬화 전 클라이언트 및 서버 컴포넌트.png]]
##### 3-1. 직렬화와 서버 컴포넌트(RSC)

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

##### 3-2. 직렬화와 클라이언트 컴포넌트(RCC)

- 앞서, 사용자가 특정 웹사이트에 요청을 보내면, 서버는 **컴포넌트 트리를 `root`부터 직렬화된 JSON형태로 재구성하기 시작한다**고 언급했다.

- 이 직렬화 과정은 모든 컴포넌트에 대해 실행되는 것이 아니라, **RCC**인 경우 건너뛰게 된다.
	- 왜냐하면, RCC는 **클라이언트가 JS 번들을 다운로드 받은 후 해석하게 되는 위치**이며, 곧 **함수**이기 때문이다.
	- 대신 **RCC**의 경우, 직접 직렬화 과정을 거치면서 해석하는 것이 아니라 `module reference` 라고 하는 새로운 타입을 적용하여, 해당 컴포넌트의 경로를 명시함으로써 직렬화를 우회하게 된다. (RCC 영역이라고 `placeholder` 배치)

- 직렬화 작업이 끝나면, 컴포넌트 트리는 JSON 트리로 생성된다.
	- [이미지 출처 - 2ast님의 포스트](https://velog.io/@2ast/React-%EC%84%9C%EB%B2%84-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8React-Server-Component%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B3%A0%EC%B0%B0)
![[사용 이미지/Next.js/더 알아보기/직렬화 후 클라이언트 및 서버 컴포넌트.png]]

- 직렬화 작업 결과물(JSON Tree)은 이후, `Stream` 형태로 클라이언트가 전달받게 된다
	- 클라이언트는 함께 다운로드 한 `JS Bundle`을 참조하여 `module reference` 타입이 등장할 때마다 RCC를 렌더링한다.
	- 서버 측에서 직렬화하며 해석하지 못한 RCC를 마저 해석(렌더링)한 후, 결과가 DOM에 반영되면 실제 화면에 보여지게 된다.
		- 이 때 비로소, `placeholder`로서 표시만 되었던 클라이언트 컴포넌트가 다시 구성요소로서 완전히 전환되는 것이다.
![[직렬화 후 해석된 클라이언트 및 서버 컴포넌트.png]]


#### 4. RSC의 제약사항

- 위의 과정을 토대로, RSC와 RCC 사용 시, 몇 가지 제약사항이 있음을 이해하고 도출해낼 수 있다.

##### 4-1. RSC에서 RCC로 `function`과 같은 직렬화 불가능한 객체를 `prop`으로 넘겨줄 수 없다.

- RSC는 서버에서 해석되어 **직렬화된 JSON 형태**로 변환된다. 
	- 그래서 **서버 컴포넌트의 구성 요소는 직렬화 가능해야 한다**라는 전제조건이 붙는다. 

- 만약, RSC가 자식 컴포넌트인 RCC에게 함수를 `props`로 넘겨주면, 서버에 의해 해석된 서버 컴포넌트의 직렬화된 JSON 데이터에 이 사실이 명시되어야 하므로, 에러가 발생할 수 있다.
```tsx
export default function Page(){
	function eventFunc(){
		// ...
	}
	
	return <ClientComponent func={func} ... />
}
```

```tsx
 {
   $$typeof: Symbol(react.element),
   type: "div",
   props: { 
 		children: {
 		  $$typeof: Symbol(react.element),
           type: {
             $$typeof: Symbol(react.module.reference),
             name: "page",
             filename: "./src/ClientComponent.js"
           },
 		  props: {callback:function}, // 이처럼 JSON에 function이 명시되어야만 한다.
 		  ...
 		}
 	},
   ...
 }
```

- 그 외에도 아래와 같은 이유가 있다.
	- 1. **SSR과 CSR으로서의 차이**: RSC는 서버 측에서 렌더링되고 클라이언트에게 HTML 형태로 전달된다. 이에 반해 RCC는 브라우저에서 자바스크립트를 사용하여 렌더링된다. 서버와 클라이언트 간에 함수를 전달하면, 서버에서는 함수를 실행하고 결과를 HTML로 렌더링하지만, 클라이언트에서는 함수가 다시 실행되지 않고 단순히 결과만 받게 된다. 
    
	2. **클라이언트-서버 간 분리**: 서버 측과 클라이언트 측의 환경은 분리되어 있다. 서버에서 사용되는 함수는 서버 환경을 고려하여 작성되었을 수 있으며, 클라이언트 환경에서는 작동하지 않을 수 있다. 
	
	3. **보안 이슈**: 클라이언트 측에 함수를 전달하면 해당 함수가 악의적인 코드로 대체될 수 있으며, 이는 보안 문제로 이어질 수 있습니다. 사용자 입력 또는 외부 소스에서 받은 데이터를 사용하여 함수를 생성하거나 실행하는 경우, 클라이언트 측에 해당 함수를 전달하는 것은 특히 위험할 수 있다.

- 하지만, **RSC에서 다른 RSC로 함수를 넘기는 것은 문제가 없다.** 왜냐하면, RSC간의 함수 전달은 서버 환경에서 실행되는 자바스크립트 코드로 간주되기 때문이다.
	- RSC는 Node.js와 같은 서버 환경에서 실행되며, 클라이언트 환경에서는 실행되지 않는다.
		- 어차피 서버에서 렌더링되는 RSC간의 함수 전달을 굳이 클라이언트 측으로 넘기는 `Stream`에 서술할 필요가 없다.
	- 이에 따라 **RSC 간에 함수를 전달하더라도 클라이언트 측에서는 이 함수를 직접 사용할 수 없기 때문에 클라이언트에서 함수가 실행되어야 하는 상황에서 단순히 결과만 받게되는 문제가 아예 발생하지 않는 것**이다.

> **예외**
```tsx
const ServerComponent = ()=>{
  const add = async (a:number,b:number) =>{
    'use server'
    return a+b
  }

  return <div>
        <ClientComponent addFunc={add}/>
    </div>
}
```
 -  next.js의 `server action`을 사용하면 **RSC에서 RCC로 함수를 전달할 수 있다.**
	 - 위와 같이 RSC에서 `use server` 지시어와 함께 함수를 정의하면 RCC로 넘겨줄 수 있다.
	 - 이 때, 해당 함수의 `params`와 `return data`은 모두 직렬화가 가능해야 한다. 

- 이 때, RSC에서 RCC로 함수를 전달할 수 있는 이유는 **"use server" 지시문**과 관련이 있다고 예상할 수 있다.
	- **`'use server'` 지시문**: 클라이언트 컴포넌트에서 서버 컴포넌트로 함수를 전달하려면, 해당 함수가 "use server" 지시문을 사용하여 정의되어야 한다. 이렇게 하면 해당 함수가 서버 환경에서 실행될 것으로 표시되어 클라이언트 컴포넌트에서도 사용할 수 있게 되기 때문이다.
	- `server action`에 대한 자세한 내용은 [여기](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations)를 참조하자.

##### 4-2. RCC는 RSC를 직접 return해줄 수 없으며, 반드시 `children prop`의 형태로 넘겨주어야 한다.
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

- **서버**에서는 모든 RSC가 순차적으로 해석되다가, 중간에 **RCC**를 만나면 `placeholder`로 표시해두고 넘어간다고 언급했다. 
	- 즉, RCC는 서버 측에서 해석되지 않기 때문에, RCC(`<ParentClientComponent/>`) 내부에서 반환되는 **children RSC**(`<ChildServerComponent />`) 가 있다면, RSC임에도 불구하고 서버에서 해석되지 못한다. 이러한 경우 해당 RSC는 RCC와 동일하게 클라이언트에서 렌더링된다.

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
- 하지만 `children props`을 통해 **children RSC**(`<ChildServerComponent />`)를 넘기게 되면, 이야기가 다르다.
	- 사실상 `<ContainerServerComponent />`를 공통 부모로 갖고 있기 때문에, 공통 부모인 `<ContainerServerComponent />`가 서버에서 렌더링되는 시점에 **children RSC**(`<ChildServerComponent />`)도 함께 렌더링된다.


#### 5. CSR과 SSR

##### 5-1. CSR
- CSR은 서버에서 초기 렌더링 없이, 클라이언트(브라우저)에서 자바스크립트(Javascript)를 사용해 애플리케이션을 렌더링하는 방식이다.

- 전통적인 CSR의 단계는 아래와 같다.
	1. 사용자가 웹 사이트 요청을 보낸다.
	2. 서버는 **빈 HTML과 접근 가능한 JS 링크**를 응답으로 보내준다.
		- HTML, CSS, 모든 스크립트를 한 번에 불러온다.
		- 이 때, CDN에 의해 **엔드 유저의 요청에 물리적으로 가까운 서버에서** 응답해 줄 수 있다.
		
	3. 클라이언트는 접근 가능한(연결된) 자바스크립트 링크를 통해, 서버로부터 다시 자바스크립트 파일을 다운로드한다.
		- 이때, SSR 과 다르게 사용자는 화면을 볼 수 있다.
		
	4. 자바스크립트 다운로드가 완료되면, 자바스크립트가 실행되며 데이터를 위한 API가 호출된다.
	5. 서버가 API로부터의 요청에 응답한다.
	6. API로부터 받아온 data를 `placeholder` 자리에 넣어준다. 이제 페이지는 상호작용이 가능해진다.

##### 5-2. SSR
- SSR은 서버에서 컴포넌트를 해석한 다음, 요청이 들어온 매 페이지마다 서버에서 즉시 렌더링 가능한 HTML을 생성해서 클라이언트에 전달하는 방식으로 전달한다.

- 전통적인 SSR의 단계는 아래와 같다.
	1. 사용자가 웹 사이트 요청을 보낸다.
	2. 서버는 즉시 렌더링 가능한 HTML 파일을 만든다.
	3. 클라이언트는 이미 렌더링 준비가 된 HTML 파일을 받고, 즉시 HTML을 렌더링한다.
		- 그러나 사이트 자체는 조작 불가능하다. (Javascript가 읽히기 전이다.)  
		
	4. 클라이언트가 자바스크립트(`bundle`)를 다운받는다.  
		- 스크립트 다운로드 중, 사용자 조작이 발생하면, 해당 시점의 사용자 조작을 기억하고 있는다.  
		
	5. 브라우저가 Javascript 프레임워크를 실행한다.  
	6. 자바스크립트까지 성공적으로 컴파일 되면, 기억하고 있던 사용자 조작이 실행되고, 웹 페이지가 상호작용 가능해진다.


- nextjs SSR: 초기로딩시, 서버에서 html파일을 ssr방식으로 받아오고, js번들도 같이받아와(csr) 페이지 상호작용을 천천히 그려나감

- rsc1: 서버에서 스트림형식 같은 직렬화과정을 거치기 때문에 번들필요없고, RSC에서 사용하는 외부라이브러리도 번들에 포함안되기때문에 제로번들
- rsc2: 기존 getServerSideprops같은 걸로 서버에 접근했었는데, rsc자체가 서버에서 수행되므로(직렬화를 서버가해줌) 저게 필요없음
- rsc3: rsc는 최종결과물이 html이 아니라 직렬화된 스트림형식으로 데이터를 받으므로, 클라이언트에서는 스트림을 해석해 리액트 가상돔을 형성하고, 돔에서 변경된 부분만을 바꿔치기하는 재조정(reconciliation)과정을 거치기 때문에, 화면에 변경사항이 있어서 새 정보를 받아와야 하더라도, 새로운 스크린 자체를 갈아끼우는게 아니라, 기존 화면의 state 등의 context를 유지한 채로 변경사항만 선택적으로 변경할 수 있음