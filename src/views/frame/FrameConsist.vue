<template>
  <page-header-wrapper>
    <a-button type="primary" @click="onClickDetail(1)">调试用</a-button>
    <a-card :bordered="false">
      <s-table
        ref="table"
        size="default"
        rowKey="key"
        :columns="columns"
        :data="loadData"
        :alert="true"
        showPagination="auto"
      >
        <span slot="bool" slot-scope="text">
          <a-tag
            :color="text === true ? 'green': 'volcano'"
          >
            {{ text }}
          </a-tag>
        </span>
        <span slot="action" slot-scope="text, record">
          <template>
            <a @click="onClickDetail(record)">查看详情</a>
          </template>
        </span>
      </s-table>
    </a-card>
  </page-header-wrapper>
</template>

<script>
import moment from 'moment'
import { STable, Ellipsis } from '@/components'
import { getBattleList } from '@/api/manage'

const columns = [
  {
    title: 'GameID',
    dataIndex: 'GameId'
  },
  {
    title: '玩家',
    dataIndex: 'playerList'
  },
  {
    title: '版本号',
    dataIndex: 'version'
  },
  {
    title: '服务器',
    dataIndex: 'serverName'
  },
  {
    title: '开始时间',
    dataIndex: 'startTime'
  },
  {
    title: '持续时间',
    dataIndex: 'duration'
  },
  {
    title: '是否一致',
    dataIndex: 'consistStatus',
    scopedSlots: { customRender: 'bool' }
  },
  {
    title: '操作',
    dataIndex: 'action',
    width: '150px',
    scopedSlots: { customRender: 'action' }
  }
]

export default {
  name: 'FrameConsist',
  components: {
    STable,
    Ellipsis
  },
  data () {
    return {
      columns: columns,
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
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getBattleList(requestParameters)
          .then(res => {
            console.log(res.result)
            this.battleData = res.result.data
            this.columns[2].filters = [
              { text: 1.1, value: 1.1 },
              { text: 1.2, value: 1.2 },
              { text: 1.3, value: 1.3 }
            ]
            this.columns[3].filters = [
              { text: '甜食服', value: '甜食服' },
              { text: '深海服', value: '深海服' }
            ]
            return res.result
          })
      },
      selectedRowKeys: [],
      selectedRows: []
    }
  },
  computed: {
  },
  filters: {

  },
  methods: {
    onClickDetail (record) {
      console.log(record.GameId)
      console.log(process.env.VUE_APP_API_BASE_URL)
      getBattleList({})
        .then(res => {
          console.log(res.result)
          return res.result
        })
    },
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
