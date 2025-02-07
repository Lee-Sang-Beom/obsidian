- 반응형 웹(Responsive Web)과 적응형 웹(Adaptive Web)은 화면 크기나 기기에 따라 웹 페이지가 사용자에게 최적화된 화면을 제공하는 방법이다. 하지만 이 두 접근 방식은 화면을 최적화하는 방식에서 차이가 있다.

#### 1. 반응형 웹

```css
/* 데스크톱 스타일 */
body {
  font-size: 16px;
}

@media (max-width: 768px) {
  /* 태블릿 스타일 */
  body {
    font-size: 14px;
  }
}

@media (max-width: 480px) {
  /* 모바일 스타일 */
  body {
    font-size: 12px;
  }
}
```
- 반응형 웹은 **CSS의 미디어 쿼리(media query)** 를 사용하여 화면 크기와 관계없이 유동적으로 레이아웃을 변경한다.
- 이 방식은 하나의 HTML과 CSS 코드 기반을 사용해 화면 크기에 맞게 요소들이 자동으로 재배치되고 크기가 조정되므로, 사용자가 어떤 기기(모바일, 태블릿, 데스크톱 등)를 사용하든 일관된 경험을 제공할 수 있다.

> **특징**
- **유동적인 레이아웃**: 화면의 크기에 따라 요소의 크기와 위치가 비율에 맞춰 자동으로 조정된다.
- **미디어 쿼리 사용**: CSS에서 화면의 가로 너비를 기준으로 스타일을 조정하는 미디어 쿼리를 사용한다.
- **코드의 일관성**: HTML 구조가 하나만 존재하므로, 유지 보수가 용이하고 코드 중복을 줄일 수 있다.
- **처리 주체** : 반응형 웹은 **클라이언트 측**에서 화면의 상황에 따라, 요소들의 배치 변화를 처리한다.

> **장점**
- 코드가 하나만 있으므로 유지 보수가 편리하다.
- 화면 크기 변화에 유연하게 대응할 수 있어 다양한 디바이스에서 일관된 사용자 경험을 제공한다.

> **단점**
- 모든 화면 크기에서 작동하도록 고려해야 하기 때문에 구현이 복잡해질 수 있다.
	- 해상도 별로 보여질 화면을 꼼꼼히 정의해야 하니, 화면 설계 과정에서 많은 시간이 든다. (하나의 템ㅍ플릿으로 모든 기기에 대응할 수 있도록 하는 웹이기 때문)

- 화면이 커지거나 작아질 때 컨텐츠의 배치나 크기가 자연스럽게 조정되지 않으면 비효율적이거나 보기 불편할 수 있다.
 

#### 2. 적응형 웹

```html
<!-- Desktop layout -->
<div class="desktop">This is for desktop</div>

<!-- Tablet layout -->
<div class="tablet">This is for tablet</div>

<!-- Mobile layout -->
<div class="mobile">This is for mobile</div>
```

```css
/* 각 레이아웃별로 CSS를 조정 */
.desktop { display: block; }
.tablet, .mobile { display: none; }

@media (max-width: 768px) {
  .desktop { display: none; }
  .tablet { display: block; }
}

@media (max-width: 480px) {
  .tablet { display: none; }
  .mobile { display: block; }
}
```

 - 적응형 웹은 여러 개의 **고정된 레이아웃**을 만들어 놓고, 사용자가 접속한 화면 크기에 맞는 레이아웃을 선택하여 표시하는 방식이다.
 - 반응형 웹과 달리 미리 정의된 레이아웃 중에서 선택하기 때문에 특정 화면 크기별로 세부적인 최적화가 가능하다.

> **특징**
- **고정된 레이아웃 사용**: 일반적으로 몇 가지 화면 크기(예: 모바일, 태블릿, 데스크톱)에 맞춰 미리 정의된 레이아웃을 사용한다.
- **서버 기반 선택 가능**: 일부 적응형 웹 구현에서는 서버가 사용자의 화면 크기를 감지하고, 해당 크기에 맞는 HTML을 제공한다.
- **처리 주체** : 반응형 웹은 **클라이언트 측**, **서버 측** 둘 다 화면의 상황에 따라, 요소들의 배치 변화를 처리할 수 있다.
- **렌딩**: 렌딩은 적응형 웹이 특정 기기에 맞는 레이아웃을 선택하고 로딩하는 과정이다.
	- 적응형 웹에서는 화면 크기와 해상도에 따라 다양한 고정된 레이아웃을 준비해두고, 사용자가 웹 페이지에 접속할 때 그 중 하나를 선택하는 방식을 사용한다. 그리고 그 과정에서 서버나 클라이언트 측에서 기기에 맞는 버전의 사이트로 redirect가 포함된 렌딩이 진행될 수도 있다.
	
	- 렌딩 과정에서 redirect를 포함한 경우를, 다음(Daum)웹 사이트를 대상으로 간단히 설명하면 아래와 같다.
	        - pc 접속 시, **`daum.net`으로 렌딩**
	        - 모바일로 접속하면 서버가 사용자 기기를 확인하여 모바일 전용 페이지로 redirection되어서 **`m.daum.net`으로 redirect하여 렌딩**.

> **장점**
- 화면 크기에 최적화된 디자인을 제공하므로, 성능을 최적화하거나 특정 디바이스에 맞춤형 UI를 적용하기 쉽다.
- 기기별로 사용자 경험을 완벽하게 맞출 수 있다.

> **단점**
- 각 화면 크기에 맞는 레이아웃을 각각 디자인해야 하므로 유지 보수가 복잡해질 수 있다.
	- 적응형 웹은 각각의 **디바이스 유형에 따라 독립적인 템플릿**을 만들고, **각 디바이스에 맞는 페이지를 별도로 제작한 다음에 랜딩되는 웹**이기 때문이다.
	  
- 화면 크기나 해상도가 추가될 때마다 새 레이아웃을 작성해야 할 수 있다.


