<template>
  <div>
    <Card>
      <div class="search-con">
        <Input class="search-input" placeholder="请输入姓名" v-model="searchData.name"/>
        <Select class="search-col" clearable v-model="searchData.gender" placeholder="请选择性别">
          <Option
            v-for="item in $appConst.enum.user_gender"
            :value="item.label"
            :key="item.key"
          >{{ item.label }}</Option>
        </Select>
        <Select class="search-col" clearable v-model="searchData.city" placeholder="请选择城市">
          <Option v-for="item in cities" :value="item.label" :key="item.key">{{ item.label }}</Option>
        </Select>
        <Button class="search-btn" @click="$refs.tables.search()" type="primary">
          <Icon type="search"/>搜索
        </Button>
        <div class="search-action">
          <Button type="primary" @click="handleCreate()">
            <Icon/>添加
          </Button>
        </div>
      </div>
      <div class="search-actions">
        <Button type="primary" @click="handleMutiltRemove()">删除</Button>
      </div>
      <app-table
        ref="tables"
        stripe
        :columns="columns"
        :searchData="searchData"
        :options="options"
        @on-delete="handleRemove"
        @on-show="handleShow"
        @on-edit="handleEdit"
        @on-selection-change="handleSelectChange"
      ></app-table>
    </Card>
    <Modal class-name="vertical-center-modal">
      <example-edit ref="modal" :modal="modal"></example-edit>
    </Modal>
  </div>
</template>
<script>
import ExampleEdit from './edit'
import AppTable from '@/components/tables'
import { fetchPages, fetchCities, deleteApp } from '@/api/example'
export default {
  name: 'tables_page',
  data () {
    return {
      btnSetting: this.$appConst.rowBtnSetting,
      searchData: {
        name: '',
        page: 1,
        pageSize: 10,
        sort: { key: 'CreateTime', order: true }
      },
      columns: [
        { title: '全选',
          type: 'selection',
          width: 60,
          align: 'center'
        },
        { title: '姓名', key: 'name', sortable: true },
        { title: '性别', key: 'gender', enum: 'user_gender', sortable: true },
        { title: '地址', key: 'address', sortable: true },
        { title: '创建时间', key: 'createTime', sortable: true },
        {
          title: '操作',
          key: 'handle',
          options: ['delete', 'show', 'edit'],
          button: [
            (h, params, vm) => {
              return h('Button', {
                props: {
                  type: 'default',
                  size: 'small'
                },
                style: {
                  marginRight: '5px'
                },
                on: {
                  click: () => {
                    this.handleShow(params.row)
                  }
                }
              }, '自定义')
            }
          ]
        }
      ],
      options: {
        doSearch: fetchPages
      },
      cities: [],
      selectRows: [],
      modal: {
        isvisible: false,
        actionType: this.$appConst.ACTION_TYPE.CREATE,
        title: '新增'
      }
    }
  },
  components: {
    ExampleEdit,
    AppTable
  },
  methods: {
    handleCreate () {
      this.initialModal('新增', this.$appConst.ACTION_TYPE.CREATE)
    },
    handleShow (row) {
      this.initialModal('详情', this.$appConst.ACTION_TYPE.VIEW, row.id)
    },
    handleEdit (row) {
      this.initialModal('编辑', this.$appConst.ACTION_TYPE.EDIT, row.id)
    },
    initialModal (title, actionType, id) {
      this.modal.isvisible = true
      this.modal.title = title
      this.modal.actionType = actionType
      if (id) { this.modal.id = id }
      this.$refs.modal.initExample()
    },
    handleRemove (row) {
      this.deleteApps(row.id)
    },
    handleMutiltRemove () {
      console.log(this.selectRows)

      let ids = this.selectRows.map(el => {
        return el.id
      }).join(',')
      console.log(ids)
      this.deleteApps(ids)
    },
    handleSelectChange (selection) {
      this.selectRows = Array.from(selection)
    },
    searchCities () {
      fetchCities().then(res => {
        this.cities = res.data
      })
    },
    deleteApps (ids) {
      deleteApp(ids).then(res => {
        this.tableData = res.data.list// mock
        this.total = res.data.total// mock
        // this.searchExamples() 实际项目这样做
      })
    },
    initiaExample () {
      this.searchCities()
    }
  },
  mounted () {
    this.initiaExample()
  }
}
</script>
<style>
</style>
