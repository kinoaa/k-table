<template>
  <div class="app-container">
    <filter-pane :filter-data="filterData" @filterMsg="filterMsg" />
    <table-pane
      :data-source="dataSource"
      @changeSize="changeSize"
      @changeNum="changeNum"
    />
  </div>
</template>

<script>
import filterPane from '@/components/Table/filterPane'
import tablePane from '@/components/Table/tablePane'
import { dynamicShareList } from '@/api/user'
import { timeFormat } from '@/filters/index'

export default {
  name: 'DynamicShare',
  components: { filterPane, tablePane },
  data() {
    return {
      // 搜索栏配置
      filterData: {
        timeSelect: true,
        elinput: [
          {
            name: '脸影号',
            width: 230,
            key: 'userId'
          },
          {
            name: '动态ID',
            width: 230,
            key: 'dynamicId'
          },
          {
            name: '分享ID',
            width: 230,
            key: 'shareId'
          }
        ]
      },
      // 表格配置
      dataSource: {
        tool: [{
          name: '新增版本',
          key: 1,
          permission: 2010701,
          handleClick: this.handleAdd,
          bgColor:''//自定义按钮背景色
        }],
        data: [], // 表格数据
        cols: [
           {
            label: '头像',
            prop: 'headImg',
            isImagePopover: true,
            width: 70
          },
          {
            label: '动态id',
            prop: 'dynamicId',
            width: 100
          },
          {
            label: '分享id',
            prop: 'id',
            width: 100
          },
          {
            label: '分享人脸影号',
            prop: 'userId'
          },
          {
            label: '分享人昵称',
            prop: 'nickName'
          },
          {
            label: '分享的时间',
            prop: 'shareTime',
            isCodeTableFormatter: function(val) {
              return timeFormat(val.shareTime)
            }
          },
          {
            label: '过期时间',
            prop: 'expireTime',
            isCodeTableFormatter: function(val) {
              return timeFormat(val.expireTime)
            }
          },
          {
            label: '分享渠道',
            prop: 'shareChannel',
            isCodeTableFormatter: function(val) {
              if (val.shareChannel === 1) {
                return 'IOS'
              } else {
                return 'Android'
              }
            }
          },
          {
            label: '分享平台',
            prop: 'shareToWhere',
            isCodeTableFormatter: function(val) {
              if (val.shareToWhere === 1) {
                return '朋友圈'
              } else if (val.shareToWhere === 2) {
                return '微信好友'
              } else if (val.shareToWhere === 3) {
                return 'qq空间'
              } else if (val.shareToWhere === 4) {
                return 'qq好友'
              } else if (val.shareToWhere === 5) {
                return '新浪微博'
              } else if (val.shareToWhere === 6) {
                return '脸影好友'
              }
            }
          }
        ], // 表格的列数据
        handleSelectionChange: () => {},
        isSelection: false, // 表格有多选时设置
        isOperation: false, // 表格有操作列时设置
        isIndex: true, // 列表序号
        loading: true, // loading
        pageData: {
          total: 0, // 总条数
          pageSize: 10, // 每页数量
          pageNum: 1 // 页码
        },
        operation: {
          // 表格有操作列时设置
          label: '操作', // 列名
          width: '100', // 根据实际情况给宽度
          data: [ // 功能数组
            {
              type: 'icon',//为icon则是图标
              label: '推荐', // 功能
              icon: 'iconfont recommend-btn icon-iconkuozhan_tuijianpre',
              permission: '3010105', // 后期这个操作的权限，用来控制权限
              handleRow: this.handleRow
            },
            {
              label: '删除', // 操作名称
              type: 'danger',//为element btn属性则是按钮
              permission: '2010702', // 后期这个操作的权限，用来控制权限
              handleRow: this.handleRow
            }
          ]
        }
      },
      dialogAdd: false,
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
        pageNum: this.dataSource.pageData.pageNum
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
      if (this.msg.dynamicId) {
        data.dynamicId = this.msg.dynamicId
      }
      if (this.msg.shareId) {
        data.shareId = this.msg.shareId
      }
      this.dataSource.loading = true
      dynamicShareList(data).then(res => {
        this.dataSource.loading = false
        if (res.succeed) {
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
    changeSize(size) {
      this.dataSource.pageData.pageSize = size
      this.getList()
    },
    changeNum(pageNum) {
      this.dataSource.pageData.pageNum = pageNum
      this.getList()
    }
  }
}
</script>

<style  scoped lang='scss'>

</style>
