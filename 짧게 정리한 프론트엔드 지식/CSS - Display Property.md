
#### 1. display property의 정의

- `display` propertys는  CSS에서 요소의 박스 모델과 레이아웃 방식(즉, 페이지에서 요소가 어떻게 배치되고 표시되는지)을 결정하는 중요한 속성이다.
- `display` 속성은 요소를 블록 요소로 만들거나, 인라인 요소로 만들거나, 아예 화면에 표시되지 않게 할 수 있다.

#### 2. 주요 속성값

1. `display: block`
	- 요소가 블록 레벨 요소로 취급된다.
	- 블록 요소는 항상 새로운 줄에서 시작하고, 가능한 전체 가로 너비를 차지한다.
	- `width, height, margin, padding` 등의 프로퍼티 지정이 가능하다.
	- ex) `<div>`, `>p>`, `<h1>` 등
```css
div{
	display: block;
}
```

2. `display: inline`
	- 요소가 인라인 요소로 취급된다.
	- 인라인 요소는 새로운 줄에서 시작하지 않으며, 콘텐츠만큼의 너비만 차지한다.
	- 블록레벨 요소 속성 중 일부가 적용해도 반영되지 않는다. (ex: `height, width`).
	- ex) `<span>`, `<a>`, `<strong>` 등
```css
span{
	display: inline;
}
```

3. `display: inline-block`
	- 요소가 인라인 요소처럼 동작하지만, 블록 요소처럼 너비와 높이를 설정할 수 있다.
		- 인라인 레벨 요소의 배치방법을 따르면서, 내부적으로는 블록 레벨 요소의 특징을 따른다.
		- 블록 레벨 요소처럼 `width, height, margin, padding` 등의 프로퍼티 지정이 가능하다.
	
	- 여러 요소를 한 줄에 배치하면서도 크기 조절이 가능하다.
```css
button {
  display: inline-block;
}
```

4. `display: none`
	- 요소가 DOM에는 존재하지만, 화면에는 표시되지 않는다.
	- 공간도 차지하지 않으며, 화면에서 완전히 제거된다.
```css
.hidden{
	display: none;
}
```


5. `display: flex`
	- 요소를 flex container(플렉스 컨테이너)로 만들어, 자식 요소들이 flex item(플렉스 아이템)으로 배치되도록 한다.
	- flex container(플렉스 컨테이너) 내의 요소들은 유연하게 배치된다.
```css
.container {
	display: flex; 
}
```

6. `display: grid`
	- 요소를 grid container(그리드 컨테이너)로 만들어, 자식 요소들이 grid item(그리드 아이템)으로 배치되도록 한다.
	- 복잡한 레이아웃을 정의할 때 유용하다.
```css
.g_container {
	display: grid; 
}
```