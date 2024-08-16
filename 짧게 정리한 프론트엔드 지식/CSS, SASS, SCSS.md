
#### 1. CSS
- **CSS**는 웹 페이지의 특정 요소에 대한 스타일을 지정하여, 문서를 표시하는 방법을 지정하는 언어이다.
- 하지만, CSS는 규모가 커질수록 코드는 복잡해지고, 유지보수는 불편해진다는 문제가 있다.


#### 2. SCSS
- SCSS는 CSS의 전처리기(preprocessor)이다. 
	- SCSS는 "Sassy CSS"의 약자로, CSS의 기능을 확장하여 더 강력하고 유연한 스타일링을 가능하게 한다.

- SCSS는 Sass(Syntactically Awesome Style Sheets)라는 스타일 시트 전처리기의 문법 중 하나이다.


#### 3. SCSS의 주요 기능

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


#### 4. SCSS와 SASS의 차이

- **SCSS**: CSS와 문법이 비슷하며, CSS 코드가 SCSS로 그대로 작성될 수 있다.
	- SCSS는 더 많은 기능을 제공하는 스타일 시트 전처리기이다.
	
- **SASS**: SCSS와는 문법이 다르다.
	- 인덴트(indent)를 사용하여 계층 구조를 나타내며 중괄호와 세미콜론이 필요 없다.
	- 더 간결하게 작성할 수 있지만, 기존 CSS 문법과는 다르다.