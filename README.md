# table怎么使用
- 下载`filterPane.vue`、 `tablePane.vue`
- 引入使用`filterPane.vue`、 `tablePane.vue`

**`filterPane.vue`、 `tablePane.vue`这两个文件内都有配置说明书！！！**

**[table使用详细使用教程](https://juejin.cn/post/6951564460964347912)**

# filterPane.vue配置项概览
```js
// 搜索栏组件
 filterData:{
   timeSelect:true,    //是否显示日期控件
   elinput:[          
     {
       name:'姓名',    //提示语
       key:'userName'  //字段名
     }
   ],
   elselect:[
     {
       name:'部门',
       key:'department'
       option:[{
         key:1,
         value:'技术部'
       }]
     }
   ]
 }
```
# tablePane.vue配置项概览
```js
  dataSource: {
          tool:[
            {
              name: '新增用户', //按钮名称
              key: 1,  // 唯一标识符
              permission: 2010106, // 权限点
              type: '',  // 使用element自带按钮类型
              bgColor: '#67c23a', // 自定义背景色
              handleClick: this.handleAdd //自定义事件
            },
          ]
         data: [], // 表格数据
         cols: [], // 表格的列数据
         isSelection: false, // 表格有多选时设置
         handleSelectionChange:(val)=>{} //点击行选中多选返回选中数组
         isOperation: true, // 表格有操作列时设置
         isIndex: true, // 列表序号
         loading: true, // loading
         pageData: {
          total: 0, // 总条数
          pageSize: 10, // 每页数量
          pageNum: 1 // 页码
         }
         operation: {
           // 表格有操作列时设置
           label: '操作', // 列名
           width: '350', // 根据实际情况给宽度
           data: [
             {
               label: '冻结', // 操作名称
               permission:'' //权限点
               type: 'info', //按钮类型
               handleRow: function(){} // 自定义事件
             },
           ]
         }
       },
```


# 实战Demo
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

**我的博客有详细使用讲解以及细节梳理**
[table使用详细教程](https://juejin.cn/post/6951564460964347912)

![image](https://github.com/kinoaa/k-table/blob/main/k-table.png)

