```
다음의 Vue 코드가 있어.
이는 src/pages/stats/video-create/stats-video-page.tsx와 연결되어있어. 아래 vue코드를 react코드로 구현하는 것이 목표야.

> StatsVideoView
<template>
  <AdminBasicCard :title="title">
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
          <b-form-radio-group
            v-model="searchData.cateType"
            name="tab"
            buttons
            button-variant="outline-primary-light"
            class="w-100"
            :class="{ 'mb-3': !$screen.xl }"
          >
            <b-form-radio :value="CATEGORY_VALUE_TYPE.MAIN">대분류</b-form-radio>
            <b-form-radio :value="CATEGORY_VALUE_TYPE.SUB">중분류</b-form-radio>
            <b-form-radio :value="CATEGORY_VALUE_TYPE.DETAIL">소분류</b-form-radio>
          </b-form-radio-group>
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

    <template #append>
      <b-card-group deck class="mt-4">
        <StatsVideoChartItem
          id="stat-video-main"
          title="메인"
          :items="chart.mainItems"
          :is-loading="isLoading"
          show-total-value
        />
        <StatsVideoChartItem
          id="stat-video-intro"
          title="인트로"
          :items="chart.introItems"
          :is-loading="isLoading"
          show-total-value
        />
      </b-card-group>
    </template>
  </AdminBasicCard>
</template>

<script>
import { BIconSearch } from 'bootstrap-vue'
import dayjs from 'dayjs'

import apiWithAuth from '@/api/modules/authorized'
import { CATEGORY_VALUE_TYPE } from '@/consts/category'

import CommonPanel from '@/mixins/CommonPanel'
import ShowToastMixin from '@/mixins/ShowToast'

import MonthlyDatePicker from '@/components/common/search/MonthlyDatePicker.vue'
import AdminBasicCard from '@/components/layouts/AdminBasicCard.vue'
import StatsVideoChartItem from '@/components/stats/StatsVideoChartItem.vue'

export default {
  components: {
    AdminBasicCard,
    MonthlyDatePicker,
    BIconSearch,
    StatsVideoChartItem,
  },
  mixins: [CommonPanel, ShowToastMixin],
  beforeRouteEnter(to, from, next) {
    next(vm => vm.search())
  },
  data: () => ({
    isLoading: false,
    searchData: {
      seDate: dayjs().startOf('day').toDate(),
      cateType: CATEGORY_VALUE_TYPE.MAIN,
    },
    chart: {
      mainItems: [],
      introItems: [],
    },
  }),
  computed: {
    CATEGORY_VALUE_TYPE: () => CATEGORY_VALUE_TYPE,
  },
  methods: {
    //  검색
    async search() {
      this.isLoading = true

      //  검색값
      const { seDate, cateType } = this.searchData

      const { data } = await apiWithAuth
        .get(`/v-statistics/contents-statistics`, {
          params: {
            cateType,
            seDate: dayjs(seDate).format('YYYY-MM'),
          },
        })
        .catch(() => {
          this.showToast('서버에서 데이터를 받아오지 못했습니다. 다시 시도해주십시오.', { single: true })
          return {}
        })

      this.isLoading = false

      if (!data) return

      this.chart.mainItems = data.mainItems
      this.chart.introItems = data.introItems
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



> StatsVideoChartItem
<template>
  <b-card class="shadow-sm" no-body>
    <b-card-header class="pt-4 d-flex">
      <h5>
        <span class="mr-3">{{ title }}</span>
        <b-badge v-if="showTotalValue" variant="dark" class="badge-sm">{{ totalValues }}건</b-badge>
      </h5>
    </b-card-header>
    <b-card-body>
      <b-overlay :show="isLoading || items.length <= 0" bg-color="white" opacity="1" z-index="5">
        <BarChart
          :id="`${id}-chart`"
          :chart-data="chartData"
          :options="options"
          :styles="styles"
          is-axis-label-on-top
          :is-data-label="isDataLabel"
        />
        <template #overlay>
          <LoadingComp v-if="isLoading" shadow />
          <ChartNoDataComp v-else @retry="$emit('retry')" />
        </template>
      </b-overlay>
    </b-card-body>
  </b-card>
</template>

<script>
import { Tableau10 } from 'chartjs-plugin-colorschemes/src/colorschemes/colorschemes.tableau'

import ChartNoDataComp from '@/components/common/ChartNoDataComp.vue'
import BarChart from '@/components/common/charts/BarChart.vue'
import LoadingComp from '@/components/common/LoadingComp.vue'

import { id as customAxisTitle } from '@/plugins/chartjs/chartjs-custom-axis-title'

/** 차트내 data-label의 포지션을 해당 값에 따라 다르게 표시하기 위한 함수 */
const setDataLabelsOption = (ctx, lowVal, highVal) => {
  const value = ctx.dataset.data[ctx.dataIndex]
  const isTooLow = Math.max(...ctx.dataset.data) * 0.15 >= value
  return isTooLow ? lowVal : highVal
}

export default {
  components: {
    BarChart,
    LoadingComp,
    ChartNoDataComp,
  },
  props: {
    id: {
      type: String,
      default: '',
    },
    title: {
      type: String,
      default: '',
    },
    items: {
      type: Array,
      required: true,
    },
    isLoading: {
      type: Boolean,
      default: false,
    },
    isDataLabel: {
      type: Boolean,
      default: false,
    },
    showTotalValue: {
      type: Boolean,
      default: false,
    },
  },
  data: () => ({
    options: {
      indexAxis: 'y',
      scales: {
        x: {
          stacked: true,
          min: 0,
          beginAtZero: true,
          ticks: {
            precision: 0,
          },
        },
        y: {
          stacked: true,
          gridLines: {
            display: false,
          },
        },
      },
      responsive: true,
      maintainAspectRatio: false,
      plugins: {
        legend: { position: 'top' },
        tooltip: {
          mode: 'nearest',
          intersect: true,
        },
        [customAxisTitle]: {
          y: {
            display: true,
            text: '업종',
          },
        },
        datalabels: {
          anchor: 'end',
          align: ctx => setDataLabelsOption(ctx, 'right', 'left'),
          color: ctx => setDataLabelsOption(ctx, '#000', '#fff'),
          clamp: true,
          formatter: value => `${value}건`,
        },
      },
    },
  }),
  computed: {
    //  datasets 매핑처리를 위해 computed 처리
    chartData() {
      const items = this.items
      return {
        labels: items.map(it => it.values.map(it => it.cateName))?.[0],
        datasets: items.map((it, idx) => ({
          label: it.tcTemplateStyleName,
          data: it.values.map(obj => obj.count),
          backgroundColor: items.length > 1 ? Tableau10[idx] : Tableau10,
          barPercentage: 0.5,
          categoryPercentage: 1,
        })),
      }
    },

    //  items 전체 갯수
    totalValues: vm => vm.items.reduce((total, curr) => total + curr.values.reduce((tt, cur) => tt + cur.count, 0), 0),

    styles() {
      const length = this.items?.[0]?.values?.length ?? 0
      const height = Math.max(length * 32, 450)
      return {
        position: 'relative',
        height: `${height}px`,
        backgroundColor: '#fff',
      }
    },
  },
}
</script>


> BarChart
<template>
  <BarChartJs :chart-id="id" :chart-data="chartData" :chart-options="options" :styles="styles" />
</template>

<script>
//  prettier-ignore
import { BarElement, CategoryScale, Chart as ChartJS, Legend, LinearScale,PointElement, Title, Tooltip,  } from 'chart.js'
import ChartJsPluginDataLabels from 'chartjs-plugin-datalabels'
import { Bar } from 'vue-chartjs/legacy'

import canvasBackground from '@/plugins/chartjs/chartjs-canvas-background'
import customAxisTitle from '@/plugins/chartjs/chartjs-custom-axis-title'

import '@/plugins/chart.js'

ChartJS.register(Title, Tooltip, Legend, BarElement, PointElement, CategoryScale, LinearScale)

export default {
  components: { BarChartJs: Bar },
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




===============================================

[구현 시 주의사항]

1. 타입 및 api 호출을 위한 파일은 아래에 생성되어있어. 이건 다른 페이지도 동일한 구조니까 참고 꼭 해.
- src/features/stats/stats-video-model.ts: 프론트엔드 내부에서만 사용하는 타입
   > 이미 구현되어 있음
   > src/features/stats/video-create 경로에서 사용하는 데이터와 구조가 동일함
   > vue코드와 타입이름은 달라도 내용은 구현되어있어

- src/service/dto/stats-video-dto.ts: service 파일에서 백엔드 통신과만 직접적으로 사용하는 타입
   > 이미 구현됨
   > vue코드와 타입이름은 달라도 내용은 구현되어있어

- src/types/stats-common.ts: 위 2개타입파일에서 공통적으로 참조해야하는 요소가 위치한 곳 
- src/service/stats-service.ts: 백엔드 통신을 위한 코드  
- src/features/stats/video/hooks/queries/use-stats-video-query-actions.ts
   > 백엔드 통신을 위해 구현된 서비스파일을 호출하는 훅
   > 이미 구현되어 있으므로 데이터 조회 시 이걸 참고하면 됨
   
- src/features/stats/stats-video-constants.ts: stats/video와 stats/video-create 경로에서 함께 사용하는 상수값
- src/features/stats/stats-video-handlers.ts: 해당 페이지에서만 사용하는 유틸성격 함수

  
  
2. 구현 시 참고할만한 것  
- 현재 개발해야 하는경로는 src/pages/stats/video/stats-video-page.tsx야.
- 이미 비슷하게 구현된 코드가 있어. (src/pages/stats/video-create/stats-video-create-page.tsx)
   > 구현 시, 해당 코드파일을 꼭 참고하여 개발할 것
   > 검색구현부터 화면 표시까지 거의 유사해.
   > 해당 페이지에서 api를 통해 response로 받은 데이터와 구현해야 할 페이지에서 response로 받는 데이터의 구조는 동일해
 
- useCategory가 필요하다면, 이미 있는 파일(src/lib/category-parser.ts)이 있어.
- category.js 관련내용이 필요하다면, src/data/category-constants.ts를 확인해
- loadingcomp 관련내용이 필요하다면 src/components/loader 하위에 정의된 컴포넌트를 확인해
- 차트는 현재 shadcn-ui의 chart 컴포넌트(src/components/ui/chart)를 사용하면 돼.
   > 이것도 마찬가지로 src/pages/stats/video-create/stats-video-create-page.tsx와 연결된 코드가 있어.
   > src/features/stats/video-create/components/stats-video-create-chart-body.tsx를 참고하면 될거야.

- vue의 AdminBasicCard는 구현할 필요는 없음 (이미 다른 react코드는 없음.)
```

