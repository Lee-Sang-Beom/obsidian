```
다음의 Vue 코드가 있어.
이는 src/pages/stats/contents/stats-contents-page.tsx 요소부터 구현해야할 요소야.

> StatsContentsView
<template>
  <AdminBasicCard :title="title">
    <b-row class="mb-4">
      <b-col cols="12">
        <b-form id="search-form" @submit.prevent="search">
          <b-form-row class="mb-3">
            <b-col cols="12" xl="1" class="align-self-center">
              <label :class="{ 'mb-0': $screen.xl }" for="date-picker">기간</label>
            </b-col>
            <b-col cols="12" xl="11">
              <MonthlyDatePicker id="date-picker" v-model="searchData.seDate" is-past-now-only />
            </b-col>
          </b-form-row>
          <b-form-row>
            <b-col cols="12" xl="1" class="align-self-center">
              <label :class="{ 'mb-0': $screen.xl }" for="category-select">카테고리</label>
            </b-col>
            <b-col cols="12" xl="8">
              <VringoCategorySelect
                id="category-select"
                v-model="searchData.cateId"
                :mode="CATEGORY_TYPE.SUB"
                :class="{ 'mb-3': !$screen.xl }"
              />
            </b-col>
            <b-col cols="12" xl="3" class="align-self-end">
              <b-button
                id="searchButton"
                block
                type="submit"
                variant="primary"
                class="btn-wth-icon icon-left"
                :disabled="isLoading"
              >
                <span class="icon-label">
                  <b-icon-search />
                </span>
                <span class="btn-text"> 검색 </span>
              </b-button>
            </b-col>
          </b-form-row>
        </b-form>
      </b-col>
    </b-row>
    <b-row>
      <b-col cols="12">
        <StatsContentsChartBody :labels="chart.labels" :items="chart.items" :is-loading="isLoading" @retry="search" />
      </b-col>
    </b-row>
  </AdminBasicCard>
</template>

<script>
import { BIconSearch } from 'bootstrap-vue'
import dayjs from 'dayjs'

import apiWithAuth from '@/api/modules/authorized'
import { CATEGORY_TYPE } from '@/consts/category'

import CommonPanel from '@/mixins/CommonPanel'
import ShowToastMixin from '@/mixins/ShowToast'

import MonthlyDatePicker from '@/components/common/search/MonthlyDatePicker.vue'
import VringoCategorySelect from '@/components/common/search/VringoCategorySelect.vue'
import AdminBasicCard from '@/components/layouts/AdminBasicCard.vue'
import StatsContentsChartBody from '@/components/stats/StatsContentsChartBody.vue'

import { useCategory } from '@/utils/CategoryParser'

export default {
  components: {
    AdminBasicCard,
    MonthlyDatePicker,
    VringoCategorySelect,
    BIconSearch,
    StatsContentsChartBody,
  },
  mixins: [CommonPanel, ShowToastMixin],
  beforeRouteEnter(to, from, next) {
    next(vm => vm.search())
  },
  data: () => ({
    isLoading: false,
    searchData: {
      seDate: dayjs().startOf('day').toDate(),
      cateId: 0,
    },
    chart: {
      labels: [],
      items: [],
    },
  }),
  computed: {
    CATEGORY_TYPE: () => CATEGORY_TYPE,
  },
  methods: {
    //  검색
    async search() {
      this.isLoading = true

      //  검색값
      const { seDate, cateId } = this.searchData
      const { getCateById } = useCategory()
      const { main, sub } = getCateById(cateId)

      const { data } = await apiWithAuth
        .get(`/v-statistics/image-resource-statistics`, {
          params: {
            cateMainType: main,
            cateSubType: sub,
            seDate: dayjs(seDate).format('YYYY-MM'),
          },
        })
        .catch(() => {
          this.showToast('서버에서 데이터를 받아오지 못했습니다. 다시 시도해주십시오.', { single: true })
          return {}
        })

      this.isLoading = false

      if (!data) return

      this.chart.labels = data.weekNameList
      this.chart.items = data.items
    },
  },
}
</script>


> AdminBasicCard
<template functional>
  <div :style="{ height: 'inherit' }">
    <b-card
      no-body
      class="shadow-sm mb-0"
      :class="{ ...data.staticClass }"
      :style="{ height: 'inherit' }"
      v-bind="$attrs"
    >
      <b-card-header v-if="!hideTitle && ($slots.header || props.title)" class="pt-4 pb-0">
        <slot name="header" :title="props.title">
          <h2 class="h4">{{ props.title }}</h2>
        </slot>
      </b-card-header>
      <b-card-body>
        <slot />
      </b-card-body>
    </b-card>
    <slot name="append" />
  </div>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      default: '',
    },
    hideTitle: {
      type: Boolean,
      default: false,
    },
  },
}
</script>


> VringoCategorySelect
<template>
  <b-input-group>
    <b-form-select
      v-if="visibleFlag >= CATEGORY_VALUE_TYPE.MAIN"
      id="main-category-selector"
      :value="main"
      class="h-100"
      :options="mainCateOptions"
      :disabled="disabled"
      :state="mainState"
      @change="val => (main = val)"
    />
    <b-form-select
      v-if="visibleFlag >= CATEGORY_VALUE_TYPE.SUB"
      id="sub-category-selector"
      :value="sub"
      class="h-100"
      :options="subCateOptions"
      :disabled="disabled"
      :state="subState"
      @change="val => (sub = val)"
    />
    <b-form-select
      v-if="visibleFlag >= CATEGORY_VALUE_TYPE.DETAIL"
      id="detail-category-selector"
      :value="detail"
      class="h-100"
      :options="detailCateOptions"
      :disabled="disabled"
      :state="detailState"
      @change="val => (detail = val)"
    />
  </b-input-group>
</template>

<script>
import { CATEGORY_TYPE, CATEGORY_VALUE_TYPE, ETC_CATEGORY_SEQ } from '@/consts/category'

import { useCategory } from '@/utils/CategoryParser'

const { mainCateList, subCateList, detailCateList, getCateId, getCateById } = useCategory()

export default {
  model: {
    prop: 'id',
    event: 'input',
  },
  props: {
    //  v-model (cateId)
    id: {
      type: Number,
      required: true,
    },
    //  선택가능한 카테고리 범위 (ex, 중분류라면 대/중분류까지만 선택 가능)
    mode: {
      type: String,
      default: CATEGORY_TYPE.DETAIL,
    },
    //  input disabled 속성
    disabled: {
      type: Boolean,
      default: false,
    },
    //  전체 선택 여부
    isAllDisabled: {
      type: Boolean,
      default: false,
    },
    //  기타 카테고리 선택가능 여부
    isOthersEnabled: {
      type: Boolean,
      default: false,
    },
    state: {
      type: [Boolean, null],
      default: null,
    },
  },
  computed: {
    CATEGORY_VALUE_TYPE: () => CATEGORY_VALUE_TYPE,

    //  셀렉트 표시 범위
    visibleFlag() {
      switch (this.mode) {
        case CATEGORY_TYPE.DETAIL:
          return CATEGORY_VALUE_TYPE.DETAIL
        case CATEGORY_TYPE.SUB:
          return CATEGORY_VALUE_TYPE.SUB
        case CATEGORY_TYPE.MAIN:
          return CATEGORY_VALUE_TYPE.MAIN
        default:
          return 0
      }
    },

    //  카테고리 객체
    category: vm => getCateById(vm.id) ?? { main: 0, sub: 0, detail: 0 },

    main: {
      get() {
        return this.category?.main ?? 0
      },
      set(main) {
        this.updateValue({ main })
      },
    },
    sub: {
      get() {
        return this.category?.sub ?? 0
      },
      set(sub) {
        const { main } = this.category
        this.updateValue({ main, sub })
      },
    },
    detail: {
      get() {
        return this.category?.detail ?? 0
      },
      set(detail) {
        const { main, sub } = this.category
        this.updateValue({ main, sub, detail })
      },
    },

    //  select 옵션
    mainCateOptions() {
      const mainList = mainCateList
        //  기타 카테고리 선택유무 옵션에 따른 기타 카테고리 필터링
        .filter(it => !!this.isOthersEnabled || it.seq !== ETC_CATEGORY_SEQ)
        .map(it => ({ value: it.main, text: it.name }))
      return [{ value: 0, text: '대분류 전체', disabled: this.isAllDisabled }, ...mainList]
    },
    subCateOptions() {
      return [
        { value: 0, text: '중분류 전체', disabled: this.isAllDisabled },
        ...subCateList.filter(it => it.main === this.main).map(it => ({ value: it.sub, text: it.name })),
      ]
    },
    detailCateOptions() {
      return [
        { value: 0, text: '소분류 전체', disabled: this.isAllDisabled },
        ...detailCateList
          .filter(it => it.main === this.main && it.sub === this.sub)
          .map(it => ({ value: it.detail, text: it.name })),
      ]
    },

    //  각 input별 폼검증 state
    mainState() {
      //  state가 null이 아님 == 검증실패 -> mainType 전체선택 x 값 검증
      const state = this.state !== null ? (this.isAllDisabled ? !!this.main : null) : this.state
      //  state가 true인 경우 null로 return
      return state === true ? null : state
    },
    subState() {
      //  state가 null이 아님 == 검증실패 -> subType 전체선택 x 값 검증
      const state = this.state !== null ? (this.isAllDisabled ? !!this.sub : null) : this.state
      //  state가 true인 경우 null로 return
      return state === true ? null : state
    },
    detailState() {
      return this.state
    },
  },
  watch: {
    //  비활성화시 모든값 초기화
    disabled(val) {
      if (val) {
        //  main이 변하므로 아래 watch 에서 detail도 초기화 시킴
        this.main = 0
      }
    },

    //  모드 변경시 해당 값 및으로 값 초기화
    mode(newVal) {
      switch (newVal) {
        case CATEGORY_TYPE.SUB:
          this.updateValue({ main: this.main, sub: this.sub })
          break
        case CATEGORY_TYPE.MAIN:
          this.updateValue({ main: this.main })
          break
      }
    },
  },
  methods: {
    //  업데이트
    updateValue({ main = 0, sub = 0, detail = 0 }) {
      const id = getCateId({ main, sub, detail })

      //  id값이 동일한 경우 중복 이벤트 발생 방지
      if (this.id === id) return

      this.$emit('input', id)
    },
  },
}
</script>

> category.js
//  카테고리 타입
export const CATEGORY_TYPE = Object.freeze({
  MAIN: 'main',
  SUB: 'sub',
  DETAIL: 'detail',
})

export const CATEGORY_VALUE_TYPE = Object.freeze({
  MAIN: 1,
  SUB: 2,
  DETAIL: 3,
})

//  카테고리 소분류 빈도별 구분 타입
export const CATEGORY_PRIORITY_TYPE = Object.freeze({
  ALL: 0,
  NORMAL: 1,
  HIGH: 2,
  HIGHEST: 3,
})

export const ETC_CATEGORY_SEQ = 10800000


> StatsContentsChartBody
<template>
  <div class="w-100">
    <b-overlay :show="isLoading || items.length <= 0" bg-color="white" opacity="1" z-index="5">
      <LineChart
        id="stats-contents-chart"
        :chart-data="chartData"
        :options="options"
        :styles="styles"
        is-axis-label-on-top
      />
      <template #overlay>
        <LoadingComp v-if="isLoading" shadow />
        <ChartNoDataComp v-else @retry="$emit('retry')" />
      </template>
    </b-overlay>
  </div>
</template>

<script>
import { Tableau20 } from 'chartjs-plugin-colorschemes/src/colorschemes/colorschemes.tableau'

import ChartNoDataComp from '@/components/common/ChartNoDataComp.vue'
import LineChart from '@/components/common/charts/LineChart.vue'
import LoadingComp from '@/components/common/LoadingComp.vue'

import { id as customAxisTitle } from '@/plugins/chartjs/chartjs-custom-axis-title'

export default {
  components: {
    LineChart,
    LoadingComp,
    ChartNoDataComp,
  },
  props: {
    labels: {
      type: Array,
      required: true,
    },
    items: {
      type: Array,
      required: true,
    },
    isLoading: {
      type: Boolean,
      default: false,
    },
    title: {
      type: String,
      default: '',
    },
  },
  data: vm => ({
    options: {
      scales: {
        x: {
          offset: true,
          grid: {
            display: false,
            offset: true,
          },
          ticks: {
            callback: (value, idx) => `${vm.labels[idx]}일`,
          },
        },
        y: {
          min: 0,
          beginAtZero: true,
          ticks: {
            precision: 0,
          },
        },
      },
      plugins: {
        legend: {
          position: 'top',
          labels: {
            usePointStyle: true,
          },
        },
        tooltip: {
          callbacks: {
            title: values => `${values?.[0]?.label ?? '?'}일`,
          },
        },
        [customAxisTitle]: {
          y: {
            display: true,
            text: '사용횟수',
          },
        },
      },
      responsive: true,
      maintainAspectRatio: false,
    },
    styles: {
      position: 'relative',
      height: `${600}px`,
      backgroundColor: '#fff',
    },
  }),
  computed: {
    //  datasets 매핑처리를 위해 computed 처리
    chartData() {
      const { labels, items } = this

      return {
        labels,
        datasets: items.map((item, index) => {
          if (!item) return {}
          //   색상 지정
          const color = Tableau20[index]

          return {
            //  값 관련
            label: item.cateName,
            data: item.values,

            //  스타일
            color,
            backgroundColor: color,
            borderColor: color,
            pointBorderColor: color,
            pointStyle: 'circle',
            fill: false,
            lineTension: 0.2,

            //  기타 커스텀 값
            cateSeq: item.cateSeq,
          }
        }),
      }
    },
  },
}
</script>


> <template>
  <LineChartJs :chart-id="id" :chart-data="chartData" :chart-options="options" :styles="styles" />
</template>

<script>
//  prettier-ignore
import { CategoryScale, Chart as ChartJS, Legend, LinearScale,LineElement, PointElement, Title, Tooltip,  } from 'chart.js'
import ChartJsPluginDataLabels from 'chartjs-plugin-datalabels'
import { Line } from 'vue-chartjs/legacy'

import canvasBackground from '@/plugins/chartjs/chartjs-canvas-background'
import customAxisTitle from '@/plugins/chartjs/chartjs-custom-axis-title'

import '@/plugins/chart.js'

ChartJS.register(Title, Tooltip, Legend, LineElement, PointElement, CategoryScale, LinearScale)

export default {
  components: { LineChartJs: Line },
  props: {
    id: {
      type: String,
      required: true,
    },
    chartData: {
      type: Object,
      required: true,
    },
    options: {
      type: Object,
      required: true,
    },
    styles: {
      type: Object,
      default: () => ({}),
    },
    isDataLabel: {
      type: Boolean,
      default: false,
    },
    isAxisLabelOnTop: {
      type: Boolean,
      default: false,
    },
  },
  mounted() {
    //  각 데이터 point에 라벨 추가 (옵션)
    if (this.isDataLabel) {
      ChartJS.register(ChartJsPluginDataLabels)
    } else {
      ChartJS.unregister(ChartJsPluginDataLabels)
    }

    //  x/y축 라벨을 축끝에 표시
    if (this.isAxisLabelOnTop) {
      ChartJS.register(customAxisTitle)
    } else {
      ChartJS.unregister(customAxisTitle)
    }

    //  캔버스 배경색 설정
    ChartJS.register(canvasBackground(this.styles?.backgroundColor))
  },
}
</script>


> LoadingComp
<template>
  <b-card class="text-center" :class="{ 'shadow-lg': shadow, border: border }">
    <b-spinner
      :variant="loadingVariant"
      animated
      :style="{
        cursor: 'wait',
      }"
    />
    <h6 class="mt-2 font-weight-normal">
      <slot>
        {{ loadingLabel }}
      </slot>
    </h6>
  </b-card>
</template>

<script>
export default {
  props: {
    shadow: {
      type: Boolean,
      default: false,
    },
    border: {
      type: Boolean,
      default: false,
    },
    loadingLabel: {
      type: String,
      default: '로딩중입니다.',
    },
    loadingVariant: {
      type: String,
      default: 'primary',
    },
  },
}
</script>


> ChartNoDataComp
<template>
  <div class="text-center text-dark">
    <p class="display-4 mb-2">
      <BIconFileEarmarkX />
    </p>
    <p class="mb-2">
      {{ text }}
    </p>
    <b-button v-if="!hideButton" size="sm" variant="outline-primary" pill @click.stop="doRetry">
      {{ buttonLabel }}
    </b-button>
  </div>
</template>

<script>
import { BIconFileEarmarkX } from 'bootstrap-vue'

export default {
  components: {
    BIconFileEarmarkX,
  },
  props: {
    text: {
      type: String,
      default: '조회 결과가 없습니다.',
    },
    hideButton: {
      type: Boolean,
      default: false,
    },
    buttonLabel: {
      type: String,
      default: '다시 시도',
    },
  },
  methods: {
    doRetry() {
      this.$emit('retry')
    },
  },
}
</script>


===============================================

[구현 시 주의사항]

1. 타입 및 api 호출을 위한 파일은 아래에 생성되어있어. 이건 다른 페이지도 동일한 구조니까 참고 꼭 해.
- src/features/stats/contents/stats-contents-model.ts: 프론트엔드 내부에서만 사용하는 타입  
- src/service/dto/stats-contents-dto.ts: service 파일에서 백엔드 통신과만 직접적으로 사용하는 타입  
- src/types/stats-common.ts: 위 2개타입파일에서 공통적으로 참조해야하는 요소가 위치한 곳 
   
- src/service/stats-service.ts: 백엔드 통신을 위한 코드  
  
- src/features/stats/hooks/mutations/use-stats-contents-query-actions.ts: 백엔드 통신을 위해 구현된 서비스파일을 호출하는 훅  
- src/features/stats/stats-contents-constants.ts: 해당 페이지에서만 사용하는 상수값
- src/features/stats/stats-contents-handler.ts: 해당 페이지에서만 사용하는 유틸성격 함수

  
  
2. 구현 시 참고할만한 것  
- 이미 비슷하게 구현된 코드가 있다.
   > src/pages/settings/video-detail/settings-video-detail-page.tsx나 settings-video-status-page.tsx 와 같은 페이지와 연결된 코드파일들을 참고할 수 있다.  
   > 검색필터의 경우에도 해당 코드를 참고하면 될 거야. (해당 페이지에서는 검색필터의 선택일자가 1개만선택가능하며 월단위로 선택가능하기에 src/components/filter/date-filter.ts를 사용하면되고 granularity를 month로 지정하면된다.)

- useCategory의 경우 이미 있는 파일(src/lib/category-parser.ts)이 있어.
- category.js 관련내용이 필요하다면, src/data/category-constants.ts를 확인해
   
```

