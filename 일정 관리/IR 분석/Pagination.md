
#### Pagination 사용하기

- 본 프로젝트에서는, 보편적으로 `new Tritgion.Section`박스를 만든 후,  `Triton.Pager`를 해당 박스에`appendTo()`하여 Pager를 추가하는 것이 주로 사용되는 모습으로 보인다.

- 사용방법과 페이지 예시는 아래와 같다.
	- 본 예시에서 연결된 **팝업 리소스**는 엑셀 다운로드 예시파일과 연결된다.

```javascript
(function () {
  return {
    onInit: function (j) {
      var page = this;

      var container = new Triton.Container({
        appendTo: j,
      });
      var section = new Triton.Section({
        appendTo: container,
      });
      new Triton.Title({
        appendTo: section,
        content: PageConstructor.getCurrentMenuName(),
      });

      page.totalCountSection = new Triton.Section({
        theme: Triton.Section.Theme.Category,
        appendTo: section,
        css: { "margin-top": "10px" },
      });

      var tab = (page.tab = new Triton.Tab({
        appendTo: container,
        theme: Triton.Tab.Theme.Normal,
        contextmenuDisabled: true,
        addButtonHidden: true,
        listButtonHidden: true,
        moveButtonHidden: true,
        readOnly: false,
        css: { "margin-top": "20px" },

        // 탭 버튼 추가될 때
        onAdd: function (options) {},

        // 탭 선택 되엇을 때
        onBind: function (options, index) {
          PageManager.cpcpm({ index: index });

          page.tab.setTabIndex(PageManager.pcd(0, "index"), true);
        },
      }));
      page.tab.addTabButton(
        {
          name: "현황",
        },
        true,
        true
      );
      page.tab.addTabButton(
        {
          name: "세부현황",
        },
        true,
        true
      );

      page.bodySection = new Triton.Section({
        appendTo: container,
        css: {
          padding: "20px 20px 20px 20px",
          border: "1px solid " + TritonTheme.LIST_TABLE_BORDER_COLOR,
          "border-top": "none",
        },
      });

      page.resultSection = new Triton.Section({
        // 현황
        appendTo: page.bodySection,
        css: {
          overflow: "hidden",
        },
      });

      // 현재 활성페이지 불러오기
      // tab style 및 기능 변경을 위해 tabIndex 변경
      page.tab.setTabIndex(PageManager.pcd(0, "index"), true);
    },

    onChange: function (
      jPage,
      jPageContainer,
      i,
      parameterMap,
      beforeParameterMap
    ) {
      var page = this;

      // 비동기적으로 수행
      Requester.func(function () {
        // onInit에서 설정한 page의 resultSection 초기화
        // page의 resultSection은 tab아래의 영역으로, 즉, 메인 컨텐츠영역의 triton_section triton_content 하위 자식요소가 모두 제거된다.
        page.resultSection.empty();

        // 현재 페이지 인덱스 가져오기
        var pageIndex = PageManager.pcd(0, "index");

        if (pageIndex == 0) {
          // 현황
          page.detailPage(page.resultSection, parameterMap);
        } else {
          // 세부현황
          page.listPage(page.resultSection, parameterMap);
        }
      });
    },

    detailPage: function (appendTo, parameterMap) {
      var page = this;

      var searchSection = new Triton.Section({
        appendTo: appendTo,
      });

      var searchPanel = new Triton.Panel({
        appendTo: searchSection,
        css: { "margin-bottom": "50px" },
      });

      var searchDetailTable = new Triton.DetailTable({
        appendTo: searchPanel,
      });

      searchDetailTable.appendRow({});
      searchDetailTable.appendKeyColumn({
        content: "연도",
      });
      searchDetailTable.appendValueColumn({
        // page.yearComboBox 이름으로 ComboBox 접근
        content: (page.yearComboBox = new Triton.ComboBox({
          theme: Triton.ComboBox.Theme.Full,
          css: { width: "150px" },

          // formName: "year"
          form: { name: "year" },
          optionList: [
            {
              name: "전체",
              value: "",
            },
            {
              name: "2022년",
              value: 2022,
            },
            {
              name: "2023년",
              value: 2023,
            },
            {
              name: "2024년",
              value: 2024,
            },
            {
              name: "2025년",
              value: 2025,
            },
            {
              name: "2026년",
              value: 2026,
            },
            {
              name: "2027년",
              value: 2027,
            },
          ],
          onSelected: function (val) {
            // 현재 선택한 value값으로 관련학과를 불러옴
            page.departmentList = DepartmentList.createOptionList(val);

            Requester.func(function () {
              // 관련학과 dataList 설정
              page.departmentComboBox.setOptionList(page.departmentList);
              page.departmentComboBox.setValue(PageManager.pc("department"));
            });
          },
        })),
      });
      searchDetailTable.appendKeyColumn({ content: "학과" });
      searchDetailTable.appendValueColumn({
        content: (page.departmentComboBox = new Triton.ComboBox({
          form: { name: "department" },
          onKeyPress: function (e) {
            if (e.which == Lia.KeyCode.ENTER) {
              page.searchButton.trigger("click");
            }
          },
        })),
      });

      var searchButtonSection = new Triton.ButtonSection({
        appendTo: searchPanel,
      });

      searchButtonSection.appendToRight(
        new Triton.FlatButton({
          content: "검색 조건 초기화",
          theme: Triton.FlatButton.Theme.Normal,
          page: page,
          onClick: function (e) {
            // triton_form 클래스명을 가진 데이터 값을 언더스코어 형식으로 반환
            // 해당 페이지는 {item: '2022', department: '사회복지학과'} 와 같은 예시
            var item = FormatHelper.arrayKeyToUnderscore(
              Triton.extractFormData(page.get())
            );

            for (var key in item) {
              item[key] = "";
            }

            page.yearComboBox.setValue("");
            page.departmentComboBox.setValue("");

            // page property 1로 고정
            // 해당 페이지는 {item: '2022', department: '사회복지학과' page:1} 와 같은 예시
            item["page"] = 1;
            PageManager.cpcpm(item);
          },
        })
      );

      searchButtonSection.appendToRight(
        new Triton.FlatButton({
          content: "검색",
          theme: Triton.FlatButton.Theme.Normal,
          page: page,
          onClick: function (e) {
            var page = e.data.page;

            // 해당 페이지는 {item: '2022', department: '사회복지학과'} 와 같은 예시
            var item = FormatHelper.arrayKeyToUnderscore(
              Triton.extractFormData(page.get())
            );

            // 해당 페이지는 {item: '2022', department: '사회복지학과' page:1} 와 같은 예시
            item["page"] = 1;
            PageManager.cpcpm(item);
          },
        })
      );

      /**
       * 검색버튼으로 페이지 경로 이동 시, year와 deparment가 querystring에 세팅되어 있을것임
       * 그것을 뽑아오고 다시 세팅하는 과정
       */

      var map = FormatHelper.arrayKeyToCamel(
        Triton.extractFormData(page.get())
      );

      var searchOption = new SearchOption();
      var searchOptionList = null;

      if (String.isNotBlank(Lia.pd(null, parameterMap, "year"))) {
        // {code: constants, keyword: keyword} 형식으로 내부배열에 추가
        searchOption.add(
          TeacherManageSearchOption.YEAR,
          Lia.pd(null, parameterMap, "year")
        );
      }

      if (String.isNotBlank(Lia.pd(null, parameterMap, "department"))) {
        // {code: constants, keyword: keyword} 형식으로 내부배열에 추가
        searchOption.add(
          TeacherManageSearchOption.DEPARTMENT,
          Lia.pd(null, parameterMap, "department")
        );
      }

      if (searchOption.size() > 0) {
        // searchOption에 값이 있으면 map에 "searchOptionList" 프로퍼티 추가 작업 수행
        map["searchOptionList"] = searchOption.get();
      }

      // page, pageSize 추가
      map["page"] = Lia.pd(1, parameterMap, "page");
      map["count"] = Lia.pd(20, parameterMap, "count");

      // 예시 map 객체 결과
      // {count: 20, page: "1", searchOptionList :"[{\"code\":1,\"keyword\":\"2022\"},{\"code\":4,\"keyword\":\"문화콘텐츠학과\"}]", year: ""}
      page.yearComboBox.setValue(PageManager.pc("year"));

      LoadingPopupManager.show();

      var panel = new Triton.Panel({
        appendTo: appendTo,
      });

      var detailTable = new Triton.DetailTable({
        appendTo: panel,
      });

      if (PageManager.pc("year") == null) {
        detailTable.appendRow({});
        detailTable.appendKeyColumn({
          content: "참여학과 전임교원 수(명)</br>(A)",
          css: { width: "33.3%" },
        });
        detailTable.appendKeyColumn({
          content:
            "사업 참여 활동실적이 있는</br>사업단 참여학과 전임교원 수(명)</br>(B)",
          css: { width: "33.3%" },
        });
        detailTable.appendKeyColumn({
          content: "전임교원 참여 비율(%)</br>(C=B/Ax100)",
          css: { width: "33.3%" },
        });
        detailTable.appendRow({});
        detailTable.appendValueColumn({
          content: "0",
          css: { "text-align": "center" },
        });
        detailTable.appendValueColumn({
          content: "0",
          css: { "text-align": "center" },
        });
        detailTable.appendValueColumn({
          content: "0",
          css: { "text-align": "center" },
        });
        return;
      }

      // map 객체로 api 요청
      // 해당api는 triton예제와 다르게 api 요청시 searchOption형태와 같은 객체를 요구하는것으로보임
      Requester.awb(
        ProjectApiUrl.Project.GET_TEACHER_PARTICIPATION_STATUS_SUMMARY_LIST,
        map,
        function (status, data, request) {
          LoadingPopupManager.hide();

          if (status != Requester.Status.SUCCESS) {
            return;
          }

          detailTable.appendRow({});
          detailTable.appendKeyColumn({
            content: "참여학과 전임교원 수(명)</br>(A)",
            css: { width: "33.3%" },
          });
          detailTable.appendKeyColumn({
            content:
              "사업 참여 활동실적이 있는</br>사업단 참여학과 전임교원 수(명)</br>(B)",
            css: { width: "33.3%" },
          });
          detailTable.appendKeyColumn({
            content: "전임교원 참여 비율(%)</br>(C=B/Ax100)",
            css: { width: "33.3%" },
          });
          detailTable.appendRow({});
          detailTable.appendValueColumn({
            content: Lia.pcd(0, data, "body", "participation_professor_count"),
            css: { "text-align": "center" },
          });
          detailTable.appendValueColumn({
            content: Lia.pcd(
              0,
              data,
              "body",
              "participation_professor_status_total_count"
            ),
            css: { "text-align": "center" },
          });
          detailTable.appendValueColumn({
            content: ProjectFormatHelper.accomplishmentRate(
              Lia.pcd(0, data, "body", "participation_professor_count"),
              Lia.pcd(
                0,
                data,
                "body",
                "participation_professor_status_total_count"
              )
            ),
            css: { "text-align": "center" },
          });

          page.yearComboBox.setValue(PageManager.pc("year"));

          var currentPanel = new Triton.Panel({
            appendTo: appendTo,
            css: { "margin-top": "80px" },
          });

          var currentDetailTable = new Triton.DetailTable({
            appendTo: currentPanel,
          });

          currentDetailTable.appendRow({});
          currentDetailTable.appendKeyColumn({
            content: "단과대학(학부)",
            attr: { rowSpan: "2" },
            css: { width: "10%" },
          });
          currentDetailTable.appendKeyColumn({
            content: "학과(부,전공)",
            attr: { rowSpan: "2" },
            css: { width: "40%" },
          });
          currentDetailTable.appendKeyColumn({
            content: "전임교원 성명",
            attr: { rowSpan: "2" },
          });
          currentDetailTable.appendKeyColumn({
            content: "참여 구분",
            attr: { colSpan: "4" },
          });
          currentDetailTable.appendKeyColumn({
            content: "소개",
            attr: { rowSpan: "2" },
          });
          // currentDetailTable.appendKeyColumn({content: '삭제',attr:{rowSpan:'2'}});
          currentDetailTable.appendRow({});
          currentDetailTable.appendKeyColumn({
            content: "LINC 3.0</br>교과목 강의",
          });
          currentDetailTable.appendKeyColumn({
            content: "산업체</br>제작자 교육",
          });
          currentDetailTable.appendKeyColumn({
            content: "산학공동</br>기술개발과제 수행",
          });
          currentDetailTable.appendKeyColumn({
            content: "산학연계</br>교과목 신규 개발",
          });

          var list = Lia.p(data, "body", "list");

          // 리스트에 값이 있으면
          if (Array.isNotEmpty(list)) {
            for (var i = 0, l = list.length; i < l; i++) {
              var item = list[i];

              // row 전체에 content 없이 이벤트만 추가
              currentDetailTable.appendRow({
                css: { cursor: "pointer" },
                item: item,
                onClick: function (e) {
                  AjaxPopupManager.show(
                    ProjectPopupUrl.ASSOCIATE_PROFESSOR_DETAIL_POPUP,
                    {
                      item: e.data.item,
                    }
                  );
                },
              });
              currentDetailTable.appendValueColumn({
                content: Lia.pcd("-", item, "college"),
                css: { "text-align": "center" },
              });
              currentDetailTable.appendValueColumn({
                content: Lia.pcd("-", item, "department"),
                css: { "text-align": "center" },
              });
              currentDetailTable.appendValueColumn({
                content: Lia.pcd("-", item, "name"),
                css: { "text-align": "center" },
              });
              currentDetailTable.appendValueColumn({
                content: page.numberToFixed(Lia.pcd(0, item, "linc_count")),
                css: { "text-align": "center" },
              });
              currentDetailTable.appendValueColumn({
                content: page.numberToFixed(
                  Lia.pcd(0, item, "industrial_workers_education")
                ),
                css: { "text-align": "center" },
              });
              currentDetailTable.appendValueColumn({
                content: page.numberToFixed(
                  Lia.pcd(0, item, "industry_academia_joint_technology")
                ),
                css: { "text-align": "center" },
              });
              currentDetailTable.appendValueColumn({
                content: page.numberToFixed(
                  Lia.pcd(0, item, "new_industrial_linked_subjects")
                ),
                css: { "text-align": "center" },
              });
              currentDetailTable.appendValueColumn({
                content: "",
                css: { "text-align": "center" },
              });
            }
          } else {
            new Triton.Section({
              appendTo: panel,
              theme: Triton.Section.Theme.ListMessage,
              content: "표시될 항목이 없습니다.",
            });
          }

          // Triton.Pager Component가 위치할 Section 박스
          var pagerSection = new Triton.Section({
            // currentPanel하위 추가
            appendTo: currentPanel,

            // Triton Page 전용 theme
            theme: Triton.Section.Theme.Pager,
          });

          new Triton.Pager({
            // pageSection 하위에 추가
            appendTo: pagerSection,

            // response를 받기위해 전달한 클라이언트 request
            pageNumber: Lia.p(request, "parameterMap", "page"),
            countPerPage: Lia.p(request, "parameterMap", "count"),

            // response data에서, body 내의 total_count 접근
            totalCount: (page.responseTotalCount = Lia.p(
              data,
              "body",
              "total_count"
            )),
            onPageSelected: function (pageNumber) {
              // pageNum : 사용자가 클릭한 페이지넘버

              /**
               * @goCurrentPageWithCurrentParameterMap : 페이지 이동
               * 내부적으로 @goPage() 호출
               *
               * 기존 파라미터를 이용하여 pagination을 진행
               */
              PageManager.goCurrentPageWithCurrentParameterMap({
                page: pageNumber,
              });
            },
          });
        }
      );
    },

    listPage: function (appendTo, parameterMap) {},

    onRelease: function (j) {},

    // 페이지 객체에서 사용할 numberToFixed 메소드 정의
    // 차후 page.numberToFixed(item)으로 사용 가능
    numberToFixed: function (item) {
      var number = "-";

      if (item != 0) {
        number = item.toFixed(2);
      }

      return number;
    },
  };
})();

```