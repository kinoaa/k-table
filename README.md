# table怎么使用
- 下载`ilterPane.vue`、 `tablePane.vue`
- 引入使用`ilterPane.vue`、 `tablePane.vue`
```js
<template>
  <div class="app-container">
    <filter-pane :filter-data="filterData" @filterMsg="filterMsg" />
    <table-pane
      :data-source="dataSource"
      @changeSize="changeSize"
      @changeNum="changeNum"
    />
    <!-- <view-code :dialog-view-code="dialogViewCode" :dialog-view-code-data="dialogViewCodeData" @childMsg="childMsg" /> -->
  </div>
</template>
<script>
import filterPane from '@/components/Table/filterPane'
import tablePane from '@/components/Table/tablePane'
export default {
  name: 'Userdaily',
  components: { filterPane, tablePane },
  data() {
    return {
      // 搜索栏配置
      filterData: {
        timeSelect: true,
        elinput: [
          {
            key: 'userId',
            name: '脸影号',
            width: 230
          }
        ],
        elselect: [
          {
            name: '视频种类',
            width: 120,
            key: 'videoKind',
            option: [
              {
                key: '0',
                value: '全部'
              },
              {
                key: 1,
                value: '电影'
              },
              {
                key: 2,
                value: '剧情'
              },
              {
                key: 3,
                value: '动漫'
              }
            ]
          },
          {
            name: '审核状态',
            width: 120,
            key: 'videoStatus',
            option: [
              {
                key: '0',
                value: '全部'
              },
              {
                key: 1,
                value: '审核中'
              },
              {
                key: 2,
                value: '审核成功'
              }
            ]
          },
          {
            name: '视频来源',
            width: 120,
            key: 'source',
            option: [
              {
                key: '0',
                value: '全部'
              },
              {
                key: 1,
                value: '本地上传'
              },
              {
                key: 2,
                value: '链接分享'
              },
              {
                key: 3,
                value: '爱奇艺'
              },
              {
                key: 4,
                value: '优酷'
              },
              {
                key: 5,
                value: '腾讯'
              },
              {
                key: 6,
                value: 'b站'
              }
            ]
          }
        ]
      },
      // 表格配置
      dataSource: {
        tool: [{
          name: '添加视频',
          key: 1,
          permission: 6010202,
          type: 'success',
          handleClick: this.handleAdd
        }, {
          name: '审核视频',
          key: 2,
          permission: 6010205,
          type: 'warning',
          handleClick: this.handleAdd
        }, {
          name: '删除视频',
          key: 3,
          type: 'danger',
          permission: 6010206,
          handleClick: this.handleAdd
        }, {
          name: '显示',
          key: 4,
          type: '',
          permission: 3010104,
          handleClick: this.handleAdd
        }, {
          name: '隐藏',
          key: 5,
          type: 'info',
          permission: 6010207,
          handleClick: this.handleAdd
        }],
        data: [], // 表格数据
        cols: [
          {
            label: '视频所属者',
            prop: 'belongUserId'
          },
          {
            label: '标题',
            prop: 'title'
          },

          {
            label: '副标题(季)',
            prop: 'subtitle',
            isCodeTableFormatter: function(val) {
              if (val.subtitle === 0) {
                return '无'
              } else {
                return val.subtitle
              }
            },
            width: 100
          },
          {
            label: '发布时间',
            prop: 'releaseDate'
          },
          {
            label: '视频种类',
            prop: 'videoKind',
            isCodeTableFormatter: function(val) {
              if (val.videoKind === 1) {
                return '电影'
              } else if (val.videoKind === 2) {
                return '剧集'
              } else if (val.videoKind === 3) {
                return '动漫'
              }
            }
          },
          {
            label: '视频类型',
            prop: 'videoType'
          },
          {
            label: '状态',
            prop: 'videoStatus',
            isStatus: true
          },
          {
            label: '时长(秒)',
            prop: 'duration'
          },
          {
            label: '创建时间',
            prop: 'createTime',
            isCodeTableFormatter: function(val) {
              return timeFormat(val.createTime)
            },
            width: 150
          },
          {
            label: '来源',
            prop: 'source',
            isCodeTableFormatter: function(val) {
              if (val.source === 1) {
                return '本地上传'
              } else if (val.source === 2) {
                return '链接分享'
              } else if (val.source === 3) {
                return '爱奇艺'
              } else if (val.source === 4) {
                return '优酷'
              } else if (val.source === 5) {
                return '腾讯'
              } else if (val.source === 6) {
                return 'b站'
              }
            }
          },
          {
            label: '视频链接',
            prop: 'linkUrl',
            isTemplate: true
          },
          {
            label: '子剧集(集)',
            prop: 'amount',
            isSon: this.isSon
          },
          {
            label: '视频状态',
            prop: 'hideFlag',
            isVideoStatus: true
          },
          {
            label: '审核时间',
            prop: 'auditTime',
            isCodeTableFormatter: function(val) {
              return timeFormat(val.auditTime)
            },
            width: 150
          },
          {
            label: '审核人',
            prop: 'auditor'
          }
        ], // 表格的列数据
        handleSelectionChange: this.handleSelectionChange,
        isSelection: false, // 表格有多选时设置
        isOperation: false, // 表格有操作列时设置
        isIndex: true, // 列表序号
        loading: true, // loading
        pageData: {
          total: 0, // 总条数
          pageSize: 10, // 每页数量
          pageNum: 1 // 页码
        }
      },
      msg: {}
    }
  },
  created() {
    this.getList()
  },
  methods: {
    getList() {
      const data = {
        pageSize: this.dataSource.pageData.pageSize,
        pageNum: this.dataSource.pageData.pageNum,
        from: 1
      }
      if (this.msg.userId) {
        var reg = /^\d{9,10}$/
        if (!reg.test(this.msg.userId)) {
          this.$message({
            type: 'error',
            message: '请输入正确的脸影号'
          })
          return
        }
        data.userId = this.msg.userId
      }
      if (this.msg.beginDate) {
        data.beginDate = this.msg.beginDate
        data.endDate = this.msg.endDate
      }
      if (this.msg.videoKind > 0) {
        data.videoKind = this.msg.videoKind
      }
      if (this.msg.videoStatus > 0) {
        data.videoStatus = this.msg.videoStatus
      }
      if (this.msg.source > 0) {
        data.source = this.msg.source
      }
      this.dataSource.loading = true
      videoList(data).then(res => {
        this.dataSource.loading = false
        if (res.succeed) {
          this.dataSource.toolNumber = res.data.todayDynamicCount
          if (res.data.total > 0) {
            this.dataSource.pageData.total = res.data.total
            this.dataSource.data = res.data.data
          } else {
            this.dataSource.data = []
            this.dataSource.pageData.total = 0
          }
        }
      })
    },
    filterMsg(msg) {
      this.msg = msg
      this.getList()
    },
    childMsg(msg) {
    },
    changeSize(size) {
      this.dataSource.pageData.pageSize = size
      this.getList()
    },
    changeNum(pageNum) {
      this.dataSource.pageData.pageNum = pageNum
      this.getList()
    },
    handleAdd(name, row) {
      console.log(name, row)
    },
    handleSelectionChange(val) {
      this.selected = val
    },
    // 操作栏事件
    handleRow(index, row, lable) {
    },
    isSon(row) {
      console.log(row)
    }
  }
}
</script>

<style  scoped lang='scss'>

</style>
```
对element.ui的table组件进行二次封装，1分钟实现一个表格页面，最少的代码做最多的事

`我的博客有详细使用讲解`
[https://juejin.cn/user/166781499739358/posts](table使用详细教程)

![image](https://github.com/kinoaa/k-table/blob/main/k-table.png)
# jsonView怎么使用
引用组件传入json数据即可展示

![image](https://github.com/kinoaa/k-table/blob/main/jsonView.png)
```vue
<template>
  <div>
    <el-dialog :visible.sync="dialogViewCode" width="500px" title="详情" top="50px" :before-close="close" @close="onClose">
      <Json-view :json="dialogViewCodeData" />
      <div slot="footer">
        <el-button type="primary" @click="close">确定</el-button>
      </div>
    </el-dialog>
  </div>
</template>
<script>
import JsonView from '@/components/JsonView'
export default {
  components: { JsonView },
  props: {
    dialogViewCode: {
      type: Boolean,
      default: false
    },
    dialogViewCodeData: {
      type: Object,
      default: () => {}
    }
  },
  data() {
    return {
    }
  },
  watch: {
    dialogViewUserData(val) {
      if (val !== '') {
        console.log(val)
      }
    }
  },
  methods: {
    // 关闭dialog，重置数据
    onClose() {
      // this.$refs['addFrom'].resetFields()
    },
    // 关闭dialog
    close() {
      this.$emit('childMsg', { dialogViewCode: false })
    }
  }
}
</script>

<style  scoped lang='scss'>

</style>

```
