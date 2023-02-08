<template>
  <page-header-wrapper>
    <a-card :bordered="false">
      <s-table
        ref="table"
        size="default"
        rowKey="key"
        :columns="columns"
        :data="loadData"
        :alert="false"
        showPagination="auto"
      >
        <span slot="bool" slot-scope="text">
          <a-tag
            :color="text === true ? 'green': 'volcano'"
          >
            {{ text }}
          </a-tag>
        </span>
        <template #title>对战总览</template>
      </s-table>
    </a-card>
    <a-card :bordered="false">
      <s-table
        ref="table"
        size="default"
        rowKey="key"
        :columns="columns2"
        :data="loadData"
        :alert="false"
        showPagination="auto"
      >
        <template #title>回放总览</template>
      </s-table>
    </a-card>
    <a-card :bordered="false">
      <s-table
        ref="table"
        size="default"
        rowKey="key"
        :columns="columns3"
        :data="loadData3"
        :alert="false"
        showPagination="auto"
      >
        <template #title>对战详情</template>
      </s-table>
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getBattleDetail, getPlayerList } from '@/api/manage'

const columns = [
  {
    title: '开始时间',
    dataIndex: 'startTime'
  },
  {
    title: '持续时间',
    dataIndex: 'duration'
  },
  {
    title: '服务器',
    dataIndex: 'serverName'
  },
  {
    title: '一致性',
    dataIndex: 'consistStatus',
    scopedSlots: { customRender: 'bool' }
  },
  {
    title: '玩家列表',
    dataIndex: 'playerNames'
  }
]

const columns2 = [
  {
    title: '播放次数',
    dataIndex: 'playCounts'
  },
  {
    title: '收到不一致帧',
    dataIndex: 'inconsistentCounts'
  }
]

const columns3 = [
  {
    title: '玩家',
    dataIndex: 'playerName'
  },
  {
    title: 'App版本',
    dataIndex: 'appEdition'
  },
  {
    title: 'OS',
    dataIndex: 'os'
  },
  {
    title: 'OS版本',
    dataIndex: 'osEdition'
  },
  {
    title: '设备名',
    dataIndex: 'deviceName'
  },
  {
    title: '设备型号',
    dataIndex: 'deviceEdition'
  },
  {
    title: '设备ID',
    dataIndex: 'deviceId'
  },
  {
    title: '截止帧',
    dataIndex: 'stopFrame'
  }
]
export default {
  name: 'FrameDetails',
  components: {
    STable,
    Ellipsis
  },
  data () {
    return {
      columns: columns,
      columns2: columns2,
      columns3: columns3,
      // create model
      visible: false,
      confirmLoading: false,
      mdl: null,
      // 高级搜索 展开/关闭
      advanced: false,
      battleData: [],
      // 查询参数
      queryParam: {},
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        const gameId = this.$route.params.gameId
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getBattleDetail(requestParameters, gameId)
          .then(res => {
            console.log(res.result)
            this.battleData = res.result.data
            return res.result
          })
      },
      loadData3: parameter => {
        const gameId = this.$route.params.gameId
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getPlayerList(requestParameters, gameId)
          .then(res => {
            console.log(res.result)
            this.battleData = res.result.data
            return res.result
          })
      },
      selectedRowKeys: [],
      selectedRows: []
    }
  },
  computed: {},
  filters: {},
  methods: {
    onSelectChange (selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectedRows = selectedRows
    },
    toggleAdvanced () {
      this.advanced = !this.advanced
    },
    resetSearchForm () {
      this.queryParam = {
        date: moment(new Date())
      }
    }
  }
}
</script>
