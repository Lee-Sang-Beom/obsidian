```
다음의 Vue 코드가 있어.
이는 src/pages/site-mgmt/rendering-server/rendering-server-page.tsx와 연결되어있어. 
아래 vue코드 내용이 react코드로 정상이식되었는지 확인하는 것이 목표야.

> SitesRenderServerStatusView.vue
<template>
  <b-container fluid class="px-0" title="">
    <b-row>
      <b-col>
        <h2 class="h4">렌더링 서버 현황</h2>
        <p>마지막 업데이트 시간: {{ updatedAtString }}</p>
      </b-col>
      <b-col cols="2" lg="1" class="text-right">
        <b-button variant="dark" @click="reload">
          <b-icon-arrow-clockwise />
        </b-button>
      </b-col>
    </b-row>
    <template v-if="isLoadFailed">
      <b-overlay :show="isLoading">
        <ChartNoDataComp text="정보를 불러오지 못했습니다." @retry="reload" />
      </b-overlay>
    </template>
    <template v-else>
      <b-row class="my-4">
        <b-col v-for="server in serverList" :key="`server-${server.id}`" cols="12" lg="6" :data-addr="server.ipaddr">
          <ServerStatusInfo :server="server" @toggle="onToggleServerState" />
        </b-col>
      </b-row>
      <h2 class="h4">렌더링 큐 현황</h2>
      <b-card class="my-4 shadow-sm" body-class="p-0">
        <ServerRenderQueueTable :items="renderQueue" />
      </b-card>
    </template>
  </b-container>
</template>

<script>
import { BIconArrowClockwise } from 'bootstrap-vue'
import dayjs from 'dayjs'

import apiWithAuth from '@/api/modules/authorized'

import CommonPanel from '@/mixins/CommonPanel'

import ChartNoDataComp from '@/components/common/ChartNoDataComp.vue'
import ServerRenderQueueTable from '@/components/sites/render-server/ServerRenderQueueTable.vue'
import ServerStatusInfo from '@/components/sites/render-server/ServerStatusInfo.vue'

export default {
  components: {
    ChartNoDataComp,
    BIconArrowClockwise,
    ServerStatusInfo,
    ServerRenderQueueTable,
  },
  mixins: [CommonPanel],
  beforeRouteEnter(to, from, next) {
    next(vm => vm.loadData())
  },
  props: {
    title: {
      type: String,
      default: '',
    },
  },
  data: () => ({
    //  로딩 boolean
    isLoading: false,

    //  재실행 id
    timeoutId: null,

    //  서버 정보 array
    serverList: [],

    //  렌더링 큐 목록
    renderQueue: [],

    //  업데이트된 시간
    updatedAt: null,
  }),
  computed: {
    updatedAtString: ({ updatedAt }) => (updatedAt ? dayjs(updatedAt).format('YYYY.MM.DD HH:mm:ss') : '-'),
    isLoadFailed: ({ serverList }) => serverList.length === 0,
  },
  destroyed() {
    this.clear()
  },
  methods: {
    /** API에서 정보 요청후 반영 */
    async loadData() {
      this.isLoading = true

      await this.loadServerStatus()

      //  다음 API 요청 시도
      this.updatedAt = new Date()
      this.timeoutId = window.setTimeout(this.loadData, 60 * 1000)

      this.isLoading = false
    },

    /** API 요청 정기실행 중지 */
    clear() {
      window.clearTimeout(this.timeoutId)
      this.timeoutId = null
    },

    /** 서버 현황 정보 요청 */
    async loadServerStatus() {
      //  API 정보 요청
      const { data } = await apiWithAuth.get('/v-rserver/monitor').catch(() => ({}))

      //  내부 데이터 반영 및 갱신
      this.serverList = data?.items ?? []
      this.renderQueue = data?.renderItems ?? []
    },

    /** 재로딩 시도시 기존 정기실행 중지후 새로 로딩 */
    reload() {
      this.clear()
      this.loadData()
    },

    /** 서버 상태 토글 */
    async onToggleServerState({ id, type, value }) {
      await apiWithAuth
        .post('/v-rserver/modify-monitor', { id, activeType: type, activeValue: value })
        .catch(async () => {})

      this.reload()
    },
  },
}
</script>


> ServerStatusInfo
<template>
  <b-card no-body class="shadow-sm">
    <b-card-header
      class="pt-4 pb-4 border-bottom"
      :class="{
        'bg-gradient-success': !!server.active,
        'bg-gradient-warning': !server.active,
      }"
    >
      <div class="d-flex justify-content-between align-items-center">
        <h3 class="h5 mb-0 text-white">{{ server.id }}번 서버</h3>
        <AdminStatusBadgeComp :status="!!server.active" is-on-bg>
          {{ server.active | serverState }}
        </AdminStatusBadgeComp>
      </div>
    </b-card-header>
    <b-card-body class="p-0 pb-2">
      <b-table-simple responsive>
        <b-thead>
          <b-tr>
            <b-th></b-th>
            <b-th class="text-center">실행상태</b-th>
            <b-th class="text-center">실행중인 작업수</b-th>
            <b-th class="text-center">기능 ON/OFF</b-th>
          </b-tr>
        </b-thead>
        <b-tbody>
          <b-tr>
            <b-th>자동 렌더링</b-th>
            <b-td class="text-center">
              <AdminStatusBadgeComp :status="!!server.activeAuto">
                {{ server.activeAuto | serverState }}
              </AdminStatusBadgeComp>
            </b-td>
            <b-td class="text-center">{{ server.renderAutoCount }}</b-td>
            <b-td class="text-center">
              <b-button-group size="sm" class="btn-group-rounded">
                <b-button
                  variant="success"
                  class="px-3"
                  @click="onToggleButtonClick(SERVER_ACTIVE_TYPE.AUTO, SERVER_ACTIVE_VALUE.ACTIVE)"
                >
                  ON
                </b-button>
                <b-button
                  variant="danger"
                  class="pl-2 pr-3"
                  @click="onToggleButtonClick(SERVER_ACTIVE_TYPE.AUTO, SERVER_ACTIVE_VALUE.DEACTIVE)"
                >
                  OFF
                </b-button>
              </b-button-group>
            </b-td>
          </b-tr>
          <b-tr>
            <b-th>사용자 렌더링</b-th>
            <b-td class="text-center">
              <AdminStatusBadgeComp :status="!!server.activeUser">
                {{ server.activeUser | serverState }}
              </AdminStatusBadgeComp>
            </b-td>
            <b-td class="text-center">{{ server.renderUserCount }}</b-td>
            <b-td class="text-center">
              <b-button-group size="sm" class="btn-group-rounded">
                <b-button
                  variant="success"
                  class="px-3"
                  @click="onToggleButtonClick(SERVER_ACTIVE_TYPE.USER, SERVER_ACTIVE_VALUE.ACTIVE)"
                >
                  ON
                </b-button>
                <b-button
                  variant="danger"
                  class="pl-2 pr-3"
                  @click="onToggleButtonClick(SERVER_ACTIVE_TYPE.USER, SERVER_ACTIVE_VALUE.DEACTIVE)"
                >
                  OFF
                </b-button>
              </b-button-group>
            </b-td>
          </b-tr>
        </b-tbody>
      </b-table-simple>
    </b-card-body>
  </b-card>
</template>

<script>
import { SERVER_ACTIVE_TYPE, SERVER_ACTIVE_VALUE } from '@/consts/sites'

import AdminStatusBadgeComp from '@/components/common/AdminStatusBadgeComp.vue'

import { Dialog } from '@/utils/SweetAlertUtil'

export default {
  components: {
    AdminStatusBadgeComp,
  },
  filters: {
    serverState(val) {
      return val ? '실행중' : '정지'
    },
    serverActiveType(val) {
      switch (val) {
        case SERVER_ACTIVE_TYPE.AUTO:
          return '자동 렌더링'
        case SERVER_ACTIVE_TYPE.USER:
          return '사용자 렌더링'
        default:
          return val
      }
    },
    serverActiveValue(val) {
      switch (val) {
        case SERVER_ACTIVE_VALUE.ACTIVE:
          return '활성'
        case SERVER_ACTIVE_VALUE.DEACTIVE:
          return '비활성'
        default:
          return val
      }
    },
  },
  props: {
    server: {
      type: Object,
      required: true,
    },
  },
  computed: {
    SERVER_ACTIVE_TYPE: () => SERVER_ACTIVE_TYPE,
    SERVER_ACTIVE_VALUE: () => SERVER_ACTIVE_VALUE,
  },
  methods: {
    async onToggleButtonClick(type, value) {
      const filters = this.$options.filters

      const dialogResult = await Dialog.fire({
        //  prettier-ignore
        text: `${this.server.id}번 서버의 ${filters.serverActiveType(type)} 기능을 ${filters.serverActiveValue(value)}하시겠습니까?`,
        showCancelButton: true,
      })

      if (!dialogResult?.isConfirmed) return

      this.$emit('toggle', {
        id: this.server.id,
        type,
        value,
      })
    },
  },
}
</script>


> ServerRenderQueueTable
<template>
  <b-table
    responsive=""
    show-empty
    striped
    no-local-sorting
    table-class="text-center"
    thead-class="thead-light"
    :fields="fields"
    :items="items"
    :current-page="1"
    :tbody-tr-class="getRowClass"
  >
    <template #empty>
      <TableNotRowComp text="진행중인 렌더링 작업이 없습니다." hide-button />
    </template>

    <template #cell(progress)="{ value, item }">
      <p class="mb-1">
        <b-progress
          :value="value"
          min="0"
          max="100"
          class="progress-bar-rounded progress-bar-sm"
          :variant="isOverRunning(item) ? 'danger' : 'primary'"
          :striped="isOverRunning(item)"
        />
      </p>
      <p>
        {{ value }}%
        <b-badge v-if="isOverRunning(item)" variant="danger" pill class="ml-2">정체중</b-badge>
      </p>
    </template>

    <template #cell(renderServer)="{ value }"> {{ value }}번 서버 </template>

    <template #cell(renderType)="{ value }">
      {{ value | renderType }}
    </template>

    <template #cell(createdAt)="{ value }">
      {{ value | dayjs('YYYY-MM-DD HH:mm:ss') }}
    </template>

    <template #cell(startedAt)="{ value }">
      {{ value | dayjs('YYYY-MM-DD HH:mm:ss') }}
    </template>
  </b-table>
</template>

<script>
import dayjs from 'dayjs'

import { SERVER_ACTIVE_TYPE } from '@/consts/sites'

import TableNotRowComp from '@/components/common/table/TableNotRowComp.vue'

export default {
  filters: {
    renderType(val) {
      switch (val) {
        case SERVER_ACTIVE_TYPE.AUTO:
        case SERVER_ACTIVE_TYPE.AUTO_NUM:
          return '자동 렌더링'
        case SERVER_ACTIVE_TYPE.USER:
        case SERVER_ACTIVE_TYPE.USER_NUM:
          return '사용자 렌더링'
        default:
          return val
      }
    },
  },
  components: { TableNotRowComp },
  props: {
    items: {
      type: Array,
      default: () => [],
    },
  },
  data: () => ({
    fields: [
      { key: 'contentsId', label: 'ID', isRowHeader: true, thStyle: { width: '5%' } },
      { key: 'templateName', label: '템플릿명' },
      { key: 'progress', label: '진행도', thStyle: { width: '25%' } },
      { key: 'renderServer', label: '담당 서버', thStyle: { width: '10%' } },
      { key: 'renderType', label: '타입', thStyle: { width: '15%' } },
      { key: 'createdAt', label: '등록 시간', thStyle: { width: '10%' } },
      { key: 'startedAt', label: '시작 시간', thStyle: { width: '10%' } },
    ],
  }),
  methods: {
    isOverRunning: ({ startedAt }) => dayjs(startedAt).isBefore(dayjs().subtract(10, 'minutes')),

    getRowClass(item, type) {
      if (!item || type !== 'row') return
      if (!item.startedAt) return

      if (this.isOverRunning(item)) {
        return this.$style.overRanQueue
      }
    },
  },
}
</script>

<style lang="scss" module>
.overRanQueue {
  color: $danger;
  background-color: lighten($danger, 36%) !important;
  th {
    color: $danger;
  }
}
</style>

> sites.js
//  렌더링 서버 기능활성화토글 타입
export const SERVER_ACTIVE_TYPE = Object.freeze({
  AUTO: 'auto',
  AUTO_NUM: 1,
  USER: 'user',
  USER_NUM: 2,
})
export const SERVER_ACTIVE_VALUE = Object.freeze({
  ACTIVE: 1,
  DEACTIVE: 0,
})



===============================================

[확인사항]

1. 마지막 업데이트 시간 표시 로직이 네트워크에서 오는 게 맞는가? 아니라면 정상적으로 표시되게 변경해야 함
2. 타이머 로직이 맞는가? 
3. 한 파일에 코드가 너무 긴 경우 분리할수있는가? (ex. 페이지파일)
```

