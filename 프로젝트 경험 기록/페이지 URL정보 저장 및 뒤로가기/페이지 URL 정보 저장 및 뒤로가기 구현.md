
#### 1. Web Storage

- 서버 전송 없이 **클라이언트에 데이터를 저장할 수 있도록**, HTML5부터 추가된 저장소이다.
	- 간단한 `Key-Value` 데이터 저장 형태를 가진다.
	- 쿠키와 달리 자동 전송의 위험성이 없으며, 클라이언트에 저장만 할 뿐 서버로 전송되지 않는다.

- 쿠키와 같이 해당 도메인에 대한 데이터를 브라우저에 저장한다.
	- **쿠키**는 서버가 클라이언트에게 전송하는 데이터 파일이며, **이름, 값, 도메인 정보, 경로 정보, 만료 일자와 시간** 등을 저장한다.
	- 모든 브라우저에서 지원되지만, 매번 서버에 전송되고, 저장용량이 작고(4KB), 보안이 취약하다.

- 이러한 **쿠키 단점**을 보완하기 위해 등장한 것이 **Web Storage**이다.
	- 서버가 HTTP 헤더를 통해 스토리지 객체를 조작할 수 없다.
	- 웹 스토리지 객체 조작은 **JavaScript** 내에서만 수행가능하며, 오직 **문자형(string)** 데이터 타입만 지원한다.

- 지속성에 따라 **로컬 스토리지(Local Storage)**와 **세션 스토리지(Session Storage)로 분류할 수** 있다.
	- 같은 **Storage** 객체를 상속하기 때문에 메서드가 동일하다고 한다.

##### 1-1. 로컬 스토리지(Local Storage)

- 사용자가 데이터를 지우지 않는 이상, 브라우저나 OS를 종료해도 계속 브라우저에 남아있는 **영구성**이 특징이다.
	- 브라우저 자체에 **반영구적**으로 데이터를 저장하며, 브라우저를 종료해도 데이터가 유지된다.
	- 단, 동일한 브라우저를 사용할 때만 해당하며,  **도메인(domain)이 다른 경우** 로컬 스토리지에 접근할 수 없다.
	
- 지속적으로 필요한 데이터를 저장하므로, 자동 로그인 등에서 사용할 수 있다.

##### 1-2. 세션 스토리지(Session Storage)

- 데이터가 **브라우저 탭**에 종속(탭 윈도우 단위로 생성)되기 때문에, **윈도우나 브라우저 탭을 닫을 경우 제거**된다.
	- 각 세션마다 데이터가 개별 저장되며, 브라우저 탭을 닫을 시 데이터가 제거된다.

- 그래서 일시적으로 필요한 데이터를 저장할 때 사용한다.
	- 일회성 로그인 정보, 비로그인 장바구니, 입력 Form 저장 등에 사용될 수 있다.


#### 2. 요구사항

- 화학 알리미 프로젝트에서, **특정 데이터**에 대한 상세 페이지로 이동할 수 있는 경로가 1개가 아닐 때, **이전**으로 이동하는 동작을 분리해달라는 요구사항이 들어왔다.
	- 이 때 기존 프로젝트 로직에서는, 이전 URL이 무엇인지 **이전화면으로 이동하는 버튼이 포함된 상세 페이지**에서 기억하지 않았다.

- 기존 로직과 변경해야하는 로직은 아래와 같다.
	- 주된 변경은, `/`위치에서 상세 페이지 접근 시 리스트 화면으로 이동할 수 있게 개발되어야 한다는 점이다.

```null

1. 기존 로직
- `previousPageUrl = /`일 때, 이전화면으로 돌아갈 경우 `/`로 이동
- `previousPageUrl = `/Ci/ChemiInquiry?...`일 때, 이전화면으로 돌아갈 경우 `/Ci/ChemiInquiry?...`

2. 변경해야하는 로직
- `previousPageUrl = /`일 때, 이전화면으로 돌아갈 경우 `/Ci/ChemiInquiry`로 이동
- `previousPageUrl = `/Ci/ChemiInquiry?...`일 때, 이전화면으로 돌아갈 경우 `/Ci/ChemiInquiry?...`

```


#### 3. 이전 URL 정보(previousPageURL) 저장하기

- 예전에는 웹 브라우저에서 이전 주소를 확인하는 방법으로 `window.history` 객체를 사용하여 이전 페이지의 URL을 얻어올 수 있었다고 한다. 
	- 그러나 이 방법은 보안 상의 이유로 최근에 변경되어, 현재는 사용자가 **이전 페이지의 URL에 액세스하는 것을 제한**하고 있다고 한다.

- 그래서, 현재로서는 `recoil`, `useContext`와 같은 상태관리 라이브러리,  `hook`을 사용하거나 `localStorage`를 사용하는 방법을 선택해야 한다.
	- 여기서는 `localStorage`를 사용한 방법을 소개한다.

- 일단, 링크를 클릭하는 위치에서는, `localStorage`에 아래와 같은 정보를 저장해야 한다. 이것이 이전 URL 정보이다.
```tsx
// 1. path="/"인 Home에서 링크 클릭
<Link
  href={
	activeSeq === 0
	  ? `/`
	  : `/Ci/ChemiInquiry/ChemiInquiryDetail/${activeSeq}`
  }
  className={Style.btn_visual_more}
  prefetch={false}
  style={{ zIndex: 99999 }}
  title={buttonLinkTitle}
  onClick={() => {
    // here
	localStorage.setItem("previousPage", "/");
  }}
>
  자세히 보기{" "}
  <AiOutlineSwapRight
	size={30}
	role="img"
	aria-label="오른쪽 화살표 아이콘"
  />
</Link>


// 2. path="/Ci/ChemiInquiry" 인 위치에서 링크 클릭
<Link
	href={`${url}/${tr.chemSeq}`}
	prefetch={false}
	aria-label={`${tr.mttrnmeng} 상세보기 페이지 이동`}
	onClick={() => {
	  localStorage.setItem("previousPage", "/Ci/ChemiInquiry");
	}}
>	
	{tr.mttrnmkor}
</Link>
```


#### 4. 저장된 localStorage 값 불러오기 및 제거

- `localStorage`에 저장된 `key`를 `getItem()` 메소드와 함께 사용하면, `key`에 해당하는`value` 값을 가져올 수 있다.
	- 이 때, `localStorage`에서 값을 가져온 이후에는,  해당 `key`에 대한 `removeItem()`메소드를 사용해주어야 한다. 

```tsx
     <Button
	  title={"목록으로 이동"}
	  btnColor={"white"}
	  btnSize={"md"}
	  btnStyle={"br_6"}
	  onClick={() => {
		const previousPageUrl = localStorage.getItem("previousPage");
		if (previousPageUrl) {
		  localStorage.removeItem("previousPage");

		  if (previousPageUrl == "/") {
			router.push("/Ci/ChemiInquiry");
			router.refresh();
		  } else {
			router.back();
		  }
		} else {
		  router.back();
		}
	  }}
>
	  목록
	</Button>
```