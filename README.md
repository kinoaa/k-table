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
  name: '',
  components: { filterPane, tablePane },
  data() {
    return {
      // 搜索栏配置
      filterData: {
        timeSelect: true,
        elinput: [],
        elselect: []
      },
      // 表格配置
      dataSource: {
        tool: [],
        data: [], // 表格数据
        cols: [], // 表格的列数据
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
    //获取表格数据
    getList() {
    },
    //搜索栏事件
    filterMsg(msg) {
      this.msg = msg
      this.getList()
    },
    //子组件通信
    childMsg(msg) {
    },
    //改变每页数量
    changeSize(size) {
      this.dataSource.pageData.pageSize = size
      this.getList()
    },
    //改编页码
    changeNum(pageNum) {
      this.dataSource.pageData.pageNum = pageNum
      this.getList()
    },
    //表格上方操作行
    handleAdd(name, row) {
      console.log(name, row)
    },
    //多选回调
    handleSelectionChange(val) {
      this.selected = val
    },
    // 表格操作栏事件
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
