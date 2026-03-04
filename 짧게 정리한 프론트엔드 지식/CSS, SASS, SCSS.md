
#### 1. CSS
- **CSS**는 웹 페이지의 특정 요소에 대한 스타일을 지정하여, 문서를 표시하는 방법을 지정하는 언어이다.
- 하지만, CSS는 규모가 커질수록 코드는 복잡해지고, 유지보수는 불편해진다는 문제가 있다.


#### 2. CSS 전처리기
- SASS와 SCSS는 ==CSS를 효율적으로 작성할 수 있도록 도와주는 전처리기 언어==이다.
	- CSS의 한계를 보완하고, 가독성과 재사용성을 높여주는 역할을 한다.

- CSS 전처리기란, **CSS의 기능을 확장하여 더 효율적으로 스타일을 작성할 수 있도록 도와주는 도구**이다.
	- 전처리기는 일반 CSS보다 더 강력한 기능을 제공하고, 최종적으로는 브라우저가 이해할 수 있는 순수한 CSS로 변환한다.
	- 대표적인 CSS 전처리기가 바로 Sass(SCSS)이다.


#### 3. SCSS
- SCSS는 CSS의 전처리기(preprocessor)이다. 
	- SCSS는 "Sassy CSS"의 약자로, CSS의 기능을 확장하여 더 강력하고 유연한 스타일링을 가능하게 한다.

- SCSS는 Sass(Syntactically Awesome Style Sheets)라는 스타일 시트 전처리기의 문법 중 하나이다.


#### 4. SCSS의 주요 기능

1. **변수(Variables)**: CSS에서 자주 사용되는 값을 변수로 저장하고 재사용할 수 있다.
```scss
$primary-color: #3498db;

.button {
  background-color: $primary-color;
}
```

2. **중첩(Nesting)**: CSS 선택자를 중첩하여 작성할 수 있습니다. 이는 구조를 더 직관적으로 만들어준다.
```scss
.nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { 
    display: inline;
    margin-right: 20px;
  }

  a {
    text-decoration: none;
  }
}
```

3. **혼합(Mixins)**: 재사용 가능한 스타일 블록을 정의할 수 있으며, 매개변수를 전달할 수도 있다.
```scss
@mixin border-radius($radius) {
  border-radius: $radius;
  -webkit-border-radius: $radius;
  -moz-border-radius: $radius;
}

.box {
  @include border-radius(10px);
}
```


4. **상속(Inheritance)**: 기존의 스타일을 다른 선택자에게 상속할 수 있다.
```scss
.message {
  border: 1px solid #ccc;
  padding: 10px;
}

.success-message {
  @extend .message;
  border-color: green;
}

.error-message {
  @extend .message;
  border-color: red;
}
```

5. **연산(Operations)**: 숫자와 색상 등의 연산을 지원한다.
```scss
.container {
  width: 100% - 20px;
  padding: 10px;
  background-color: lighten(#333, 20%);
}
```


#### 5. SCSS와 SASS의 차이

- 정확히 말하면, **SCSS는 Sass의 한 문법 형식**이다.
	- Sass(Syntactically Awesome Stylesheets)는 CSS 전처리기의 이름이고, Sass에는 **두 가지 문법 스타일**이 존재한다.

1. Sass 문법
	- Indent 방식(들여쓰기)
	- `{}`나 `;` 없이 줄바꿈과 들여쓰기로 구분

```css
$primary-color: #3498db

.button
  background-color: $primary-color
  color: white
```

2. SCSS 문법
	- 일반 CSS 문법과 거의 동일
	- `{}`와 `;` 사용
```scss
$primary-color: #3498db;

.button {
  background-color: $primary-color;
}
```