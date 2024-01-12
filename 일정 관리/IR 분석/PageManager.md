
#### 목적

- 페이지 관련 정보를 불러오거나 이동을 위해 사용하는 메소드
- 사용방법 및 예시는 아래와 같다.


#### PageManager.go()

- 단순히 페이지를 이동하기 위한 메소드

```javascript
var detailListTable = new Triton.ListTable({
  appendTo: panel,
});

detailListTable.appendHeaderRow({});
detailListTable.appendHeaderColumn({ content: "항목", css: { width: "40%" } });
detailListTable.appendHeaderColumn({ content: "비율", css: { width: "20%" } });
detailListTable.appendHeaderColumn({
  content: "가중치",
  css: { width: "20%" },
});
detailListTable.appendHeaderColumn({ content: "실적", css: { width: "20%" } });

// row(tr태그)에 resultSection이라는 클래스명과 함께 속성에 indexPage라는 이름으로 value값 지정
detailListTable.appendRow({
  addClass: "resultSection",
  attr: { indexPage: 1 },
});
detailListTable.appendColumn({
  content:
    "① 교원 승진·승급·재임용 기준 점수 대비 반영된 산학협력 실적점수 평균 반영 비율(%)",
});
detailListTable.appendColumn({ content: oneGoals + "%", addClass: "goals" });
detailListTable.appendColumn({ content: "2.0", addClass: "attainment" });
detailListTable.appendColumn({
  content: oneAccomplishmentRate.toFixed(2) + "%",
  addClass: "accomplishment_rate",
});

// row(tr태그)에 resultSection이라는 클래스명과 함께 속성에 indexPage라는 이름으로 value값 지정
detailListTable.appendRow({
  addClass: "resultSection",
  attr: { indexPage: 2 },
});
detailListTable.appendColumn({
  content:
    "② 승진·승급·재임용된 교원 중 산학협력 실적 점수가 반영되어 승진.승급.재임용된 교원 비율(%)",
});
detailListTable.appendColumn({
  content: twoGoals.toFixed(2) + "%",
  addClass: "goals",
});
detailListTable.appendColumn({ content: "1.0", addClass: "attainment" });
detailListTable.appendColumn({
  content: twoGoals.toFixed(2) + "%",
  addClass: "accomplishment_rate",
});

// row(tr태그)에 resultSection이라는 클래스명과 함께 속성에 indexPage라는 이름으로 value값 지정
detailListTable.appendRow({
  addClass: "resultSection",
  attr: { indexPage: 3 },
});
detailListTable.appendColumn({
  content:
    "③ 교원 승진·승급·재임용 심사 시 산학협력 실적 점수에 반영되는 산학협력 활동요소의 다양성 비율(%)",
});
detailListTable.appendColumn({
  content: threeGoals.toFixed(2) + "%",
  addClass: "goals",
});
detailListTable.appendColumn({ content: "0.5", addClass: "attainment" });
detailListTable.appendColumn({
  content: threeAccomplishmentRate.toFixed(2) + "%",
  addClass: "accomplishment_rate",
});

// row(tr태그)에 resultSection이라는 클래스명과 함께 속성에 indexPage라는 이름으로 value값 지정
detailListTable.appendRow({
  addClass: "resultSection",
  attr: { indexPage: 4 },
});
detailListTable.appendColumn({
  content:
    "④ 교원 승진·승급·재임용 시 산학협력 실적 점수에 반영된 산학협력 활동 항목 평균 비율(%)",
});
detailListTable.appendColumn({
  content: fourGoals.toFixed(2) + "%",
  addClass: "goals",
});
detailListTable.appendColumn({ content: "0.5", addClass: "attainment" });
detailListTable.appendColumn({
  content: fourAccomplishmentRate.toFixed(2) + "%",
  addClass: "accomplishment_rate",
});
detailListTable.appendRow({});
detailListTable.appendColumn({ content: "합계", attr: { colSpan: 3 } });
detailListTable.appendColumn({
  content: isFinite(five) ? five.toFixed(2) : 0,
  addClass: "total_accomplishment_rate",
});

// resultSection 클래스명을 가진 모든 태그 불러오기
page.find(".resultSection").on("click", function () {
  // 태그별 속성값 가져오기
  var indexPage = $(this).attr("indexPage");

  if (indexPage == 1) {
    // PageManager.go() 탭 아래 영역의 컨텐츠 변경을 위한 메소드
    // argument[0](name) : 이동할 페이지
    // argument[1](parameterMap) : 이동할 페이지 내 부가적으로 들어갈 key-value 형태 값 (주 페이지URL 뒤에 key=value 형태로 붙음)
    PageManager.go(
      ["teacher_achievement_evaluation/teacher_achievement_evaluation"],
      { index: indexPage }
    );
  } else if (indexPage == 2) {
    PageManager.go(
      ["teacher_achievement_evaluation/teacher_achievement_evaluation"],
      { index: indexPage }
    );
  } else if (indexPage == 3) {
    PageManager.go(
      ["teacher_achievement_evaluation/teacher_achievement_evaluation"],
      { index: indexPage }
    );
  } else if (indexPage == 4) {
    PageManager.go(
      ["teacher_achievement_evaluation/teacher_achievement_evaluation"],
      { index: indexPage }
    );
  }

  // 활성탭 기능 및 스타일 변경을 위해 사용
  page.tab.setTabIndex(indexPage, true);
});

```


#### PageManger.cpcpm()

- URL queryString과 관련된 메소드
- 페이지 및 탭 내에 구성된 `trition_form` 내 입력 요소들을 통해 pagination property를 구성하고, 이를 포함한 페이지 이동을 가능하게 하는 메소드
- 페이지 이동의 경우, 입력요소의 formName을 underScore로 변환하는 작업을 사전적으로 요구한다.

```javascript
searchButtonSection.appendToRight(
        new Triton.FlatButton({
          content: "검색",
          theme: Triton.FlatButton.Theme.Normal,
          page: page,
          onClick: function (e) {
            // 클릭한 요소를 포함하는 page를 가져옴 (그냥 page를 써도 무방하긴 함)
            var page = e.data.page;

            // url queryString을 만들 때는 보통 property name을 언더스코어로 구성
            var item = FormatHelper.arrayKeyToUnderscore(
              Triton.extractFormData(page.get())
            );

            // 1페이지 이상에서 요청이 발생할 수도 있으니, 맨 처음 페이지로 초기화
            item["page5"] = 1;

            // 입력된 데이터를 전달하여, 페이지 이동과 동시에 url queryString을 만들어 적용하는 함수
            PageManager.cpcpm(item);
          },
        })
      );
```


#### PageManager.goCurrentPageWithCurrentParameterMap()

- 현재 `queryString` 파라미터와 API response로 들어온 `body`내 `total_count` property, 사용자 클릭 pageNumber 정보를 조합하여, `Pager`를 구성할 때 주로 사용

- 현재 페이지가 보유하고 있는 url queryString에 전달하는 object를 추가하여 페이지 이동 처리를 진행하는 방식
```javascript

if (String.isNotBlank(requestCount)) {
	var pagerSection = new Triton.Section({
	  appendTo: appendTo,
	  theme: Triton.Section.Theme.Pager,
	});
	
	// pagerSection에 Pager 추가.
	// queryString(paramterMap에서 가져옴)와 response 데이터, 사용자 클릭 pageNum을 이용해, Pager 구성
	new Triton.Pager({
	  appendTo: pagerSection,
	  pageNumber: Lia.pcd(1, parameterMap, "page5"),
	  countPerPage: requestCount,
	  totalCount: Lia.p(data, "body", "total_count"),
	  onPageSelected: function (pageNumber) {
		PageManager.goCurrentPageWithCurrentParameterMap({
		  page5: pageNumber,
		});
	  },
	});
}
  
```