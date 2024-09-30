
#### 1. 개요

![[프로젝트 경험 - 부드러운 a태그 클릭 후 스크롤 이동 (구현전).png]]

- 김해시 공공데이터 플랫폼 개발 중 좌측 연도의 HTML Element를 클릭하면 오른쪽 영역의 스크롤을 움직이게끔 하는 로직이 필요해졌다.
	- 위 이미지에서 **2010년** 부분을 클릭하면 **2010 ~ 2019년** 범위 내, 제일 최신 데이터가 있는 영역으로 자동 스크롤되도록 해야했다. 
  
- 기존에 `<a>`태그의 `href` 속성을 이용해서 특정 HTML 위치로 이동하는 작업은 완료를 했었는데, 눈깜짝할 새 이동해버리니 사용자 경험에 좋지 않은 듯 하여 이 문제에 대해 고민하게 되었다.

```tsx
 <a
	href={`#${findClosestPhotoGhSeq(
	  data.crtrYr,
	  photoGhQuery.data!
	)}`} // findClosestPhotoGhSeq 함수로 이동할 target id 찾기
	title={`${data.crtrYr}년 기준 ${selectCategoryCode.ctgrNm}데이터 표시영역으로 바로게 스크롤 이동(현재창)`}

>
{data.crtrYr}년
</a>
```


#### 2. 해결

```tsx
 <a
	// href={`#${findClosestPhotoGhSeq(
	//   data.crtrYr,
	//   photoGhQuery.data!
	// )}`} // findClosestPhotoGhSeq 함수로 이동할 target id 찾기
	href={"#"}
	title={`${data.crtrYr}년 기준 ${selectCategoryCode.ctgrNm} 데이터 표시영역으로 부드럽게 스크롤 이동(현재창)`}
	onClick={(e) => {
	  e.preventDefault(); // 기본 href 동작 방지 (부드럽게 이동하기 위해서)
	  setSelectCrtrYrData(data);

	  const targetId = findClosestPhotoGhSeq(
		data.crtrYr,
		photoGhQuery.data!
	  );
	  const targetElement =
		document.getElementById(targetId);

	  if (targetElement) {
		targetElement.scrollIntoView({
		  behavior: "smooth", // 부드럽게 스크롤
		  block: "start", // 목표 요소를 뷰포트의 상단에 맞춤
		});
	  }
	}}
>
	{data.crtrYr}년
  </a>
```
- **구현** 자체는 생각보다 간단했다. 바로 `href`는 `#`로 비워두고 `onClick` 이벤트 함수에서 `scrollIntoView` 함수로 부드럽게 스크롤을 이동하는 로직을 구현하면 되었다.
	- `e.preventDefault()`를 사용하면, `<a/>`태그의 기본 이벤트 동작이 수행되지 않도록 방지할 수 있다.
	- 여기서는, 클릭 시 링크된 URL로 이동하는 기본 동작을 멈추고, 스크롤 애니메이션을 위해 기본 링크 이동 동작을 막고, 자바스크립트를 통해 스크롤 이동을 제어할 수 있도록 했다.

- 하지만, 이에 구현이 그치면 안된다.
	- 사용자 경험을 고려한다고 스크롤 동작 방식을 바꿨지만 웹 접근성 측면에서 발생했을 수 있기 때문이다.
	- 곰곰히 이에 대해 생각해보니 `<a>` 태그의 **기본동작**을 막았으니 분명 뭔가 문제가 발생했을 것이라 생각했고, 이에 대해 자료를 찾아보았다.


#### 3. 또다른 문제와 해결

> **문제**
- `href="#"`로 `<a>` 태그의 속성을 비워두면 웹 접근성 측면에서 몇 가지 문제가 발생할 수 있다.
	- 특히 스크린 리더와 같은 보조 기술에서 링크의 동작이 명확하지 않을 수 있기 때문에, 사용자가 혼란스러워할 수 있다.
	- **`title`** 속성을 혹시 몰라 부여했지만, 스크린 리더는 종종 `title` 속성을 건너뛰거나 무시할 수 있다고 한다.

> **해결**
```tsx
<a
	href={"#"}
	role="button" // 링크를 버튼으로 인식하게 함
	aria-label={`${data.crtrYr}년 기준 ${selectCategoryCode.ctgrNm} 데이터 표시영역으로 부드럽게 스크롤 이동(현재창)`}
	title={`${data.crtrYr}년 기준 ${selectCategoryCode.ctgrNm} 데이터 표시영역으로 부드럽게 스크롤 이동(현재창)`}
	onClick={(e) => {
	  e.preventDefault(); // 기본 href 동작 방지 (부드럽게 이동하기 위해서)
	  setSelectCrtrYrData(data);

	  const targetId = findClosestPhotoGhSeq(
		data.crtrYr,
		photoGhQuery.data!
	  );
	  const targetElement =
		document.getElementById(targetId);

	  if (targetElement) {
		targetElement.scrollIntoView({
		  behavior: "smooth", // 부드럽게 스크롤
		  block: "start", // 목표 요소를 뷰포트의 상단에 맞춤
		});
	  }
	}}
>
	{data.crtrYr}년
  </a>
```

- 일단, 시각적 제목(`title`)을 스크린 리더가 건너뛸 수 있으니, 더 나은 설명을 제공할 수 있도록 `aria-label`을 사용했다.

- 지금 이 코드부분에서는, `<a>`태그를 특정 영역으로 스크롤하기 위해 사용했지, 특정 URL로 페이지를 이동시키는 고유한 동작은 진행시키지 않았다. 
	- 따라서, 링크가 실제로는 버튼과 같은 동작을 하고 있기 때문에, `role="button"`을 추가해 웹 접근성을 높이는 방법을 사용할 수 있도록 했다. (링크가 버튼처럼 동작한다!! 를 명시적으로 알려줄 수 있도록 함)

- 결과적으로, **링크가 버튼처럼 동작하게 하여 접근성을 개선**시켰고, **시각적 정보 또한 확실히 제공**되도록 하였다.
	- 그리고 원래 목표였던 부드러운 스크롤 이동 또한 정상적으로 동작하도록 했다.
![[프로젝트 경험 - 부드러운 a태그 클릭 후 스크롤 이동 (구현후).png]]