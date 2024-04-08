
#### 1. Overview

 - [Next.js14](https://nextjs.org/blog/next-14#nextjs-learn-course)버전에 대한 업데이트 내용을 보던 중 **server-action(stable)** 이라는 내용이 문득 눈에 띄었다. 
	 - 안정화되지 않았을 때에는 "오, 이런 게 나왔구나"라고만 하고 넘어갔기 때문에, 포스트를 작성하는 이 시점까지도 이게 뭔지 참 궁금했다.
 
 - 이 참에 사용해보면서 해당 기능에 대해 알아보고, 거기다 기록까지 해 두면 좋을 것 같아 포스트를 작성하게 되었다.

- 참고링크는 다음과 같다.
	- [공식 문서](https://nextjs.org/docs/app/building-your-application/data-fetching/server-actions-and-mutations)


#### 2. Server Actions and Mutations

- Next.js의 공식문서에서는 ***Server Actions***에 대해 아래와 같이 소개하고 있다.
> - ***Server Actions***은 서버에서 실행되는 비동기 함수입니다. 
> > 이는 Next.js 애플리케이션에서 Form 제출과 데이터 변경을 다루는 데 사용될 수 있습니다.


- 서버 액션은 React의 "use server" 지시문을 사용하여 정의될 수 있습니다. 이 지시문을 비동기 함수의 맨 위에 배치하여 해당 함수를 서버 액션으로 표시하거나, 별도의 파일의 맨 위에 배치하여 해당 파일의 모든 내보내기를 서버 액션으로 표시할 수 있습니다.

서버 컴포넌트(Server Components) 서버 컴포넌트는 인라인 함수 레벨 또는 모듈 레벨의 "use server" 지시문을 사용할