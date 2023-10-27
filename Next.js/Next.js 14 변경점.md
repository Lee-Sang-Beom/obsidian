
### [Next.js 14](https://nextjs.org/blog/next-14)
- **Turbopack**: 이번 Next.js14 Release에는, Turbopack 기반의 Next 컴파일러가 포함되어 있다. 그 결과로 개발 서버의 성능이 아래와 같이 향상되었다고 언급하고 있다.
	- 로컬 서버 시작 속도가 최대 53.3% 빨라짐
	- 개발 서버가 53%의 더 빨라지도 업데이트는 94% 정도 빨라짐
- Server Actions: Next.js 13 에서 소개 된 Server Actions 의 안정화 버전 포함. 서버-컴포넌트에서 상황에 따라 form 의 action 이나 React 의 formAction 으로 사용 가능
- Partial Prerendering: 기본 응답 데이터와 스트리밍 동적 컨텐츠 전달 기능을 React 의 Suspend 기반 위에서 구현. 현재 Preview 단계
- Next.js Learn: App Router, 인증, 데이터 베이스를 포함한 새로운 학습 코스