# table怎么使用

1. 下载`filterPane.vue`、 `tablePane.vue`

2. 引入使用`filterPane.vue`、 `tablePane.vue`

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
       key:'userName'，  //字段名
       width:100
     }
   ],
   elselect:[
     {
       name:'部门',
       key:'department',
       width:100
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
         selectable: function(val) { // 禁用部分行多选
          if (val.hideFlag === 1) {
            return false
          } else {
            return true
          }
        },
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

# tablePane.vue配置项Cols详解
- 普通列
```js
cols:[
   {
       label: '标题',
       prop: 'title',
       width: 200
    }
]
```
- 普通列字体颜色改变
```js
cols:[
  {
    label: '状态',
    prop: 'status',
    isTemplate: function(val) {
      if (val === 1) {
        return '禁言中'
      } else {
        return '已解禁'
      }
    },
    isTemplateClass: function(val) {
      if (val === 1) {
        return 'color-red'
      } else {
        return 'color-green'
      }
    }
  }
]
```
- 带filter过滤器列
```js
cols:[
   {
      label: '推送时间',
      prop: 'pushTime',
      isCodeTableFormatter: function(val) {
        return timeFormat(val.pushTime)
      }
    },
    {
      label: '状态',
      prop: 'status',
      isCodeTableFormatter: function(val) {
        if(val.status===1){
          return '成功'
        }else{
          return '失败'
        }
      }
    }
]
```
- 带图标列
```js
cols:[
  {
     label: '目标类型',
     prop: 'targetType',
     isIcon: true,
     filter: function(val) {
       if (val === 4) {
         return '特定用户'
       } else if (val === 3) {
         return '新注册用户'
       } else if (val === 2) {
         return '标签用户'
       } else if (val === 1) {
         return '全部用户'
       }
     },
     icon: function(val) {
       if (val === 4) {
         return 'el-icon-mobile'
       } else {
         return false
       }
     },
     handlerClick: this.handlerClick
   }
]
```
 # 操作列按钮配置详解 operation
  - 类型         *`Object`*
 - 默认值       **`{ }`**
 配置操作列
  ```js
  dataSource: {
         operation: {
           // 表格有操作列时设置
           label: '操作', // 列名
           width: '350', // 根据实际情况给宽度
           data: [
             {
               label: '修改', // 操作名称
               permission:'1001' //权限点
               type: 'info', //按钮类型
               handleRow: function(){} // 自定义事件
             },
             {
               label: '修改', // 操作名称
               permission:'1001' //权限点
               type: 'icon', //按钮类型icon为图表类型
               icon:'el-icon-plus'
               handleRow: function(){} // 自定义事件
             }
           ]
         }
  }
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
import { list } from '@/api/user'
export default {
  name: '',
  components: { filterPane, tablePane },
  data() {
    return {
      // 搜索栏配置
      filterData: {
        timeSelect: true,//时间组件
        elinput: [{
            key: 'userId',
            name: '脸影号',
            width: 230
          }],
        elselect: [{
          name: '默认排序',
          width: 120,
          key: 'sortType',
          option: [
            {
              key: 1,
              value: '默认排序'
            },
            {
              key: 2,
              value: '金币降序'
            },
            {
              key: 3,
              value: '余额降序'
            }
          ]
        }]
      },
      // 表格配置
      dataSource: {
        tool: [
         {
            name: '一键复制手机号',//按钮名称
            type:'',//默认primary，（非必须）
            key: 1,//唯一标识符
            permission: 2010106,//权限点
            bgColor: '#67c23a', // 自定义背景色（非必须）
            handleAdd: this.handleAdd//回调事件
          }
        ],  //表格上方工具栏
        data: [], // 表格数据
        cols: [
        //普通列
           {
               label: '标题',                       //列名
               prop: 'belongUserId',                //字段名称
               width: 100                           //列宽度
            },
            //带过滤器列
            {
               label: '副标题(季)',
               prop: 'subtitle',
               isCodeTableFormatter: function(val) {//过滤器
                 if (val.subtitle === 0) {
                   return '无'
                 } else {
                   return val.subtitle
                 }
               },
               width: 100
            },
            //事件列timeFormat自己写
             {
               label: '创建时间',
               prop: 'createTime',
               isCodeTableFormatter: function(val) {//时间过滤器
                 return timeFormat(val.createTime)
               },
               width: 150
             },
             //普通列字体颜色改变
              {
               label: '状态',
               prop: 'status',
               isTemplate: function(val) {
                 if (val === 1) {
                   return '禁言中'
                 } else {
                   return '已解禁'
                 }
               },
               isTemplateClass: function(val) {
                 if (val === 1) {
                   return 'color-red'
                 } else {
                   return 'color-green'
                 }
               },
               //带图标列
               {
                label: '目标类型',
                prop: 'targetType',
                permission:'',//需要的自己加权限点
                isIcon: true,
                filter: function(val) {
                  if (val === 4) {
                    return '特定用户'
                  } else if (val === 3) {
                    return '新注册用户'
                  } else if (val === 2) {
                    return '标签用户'
                  } else if (val === 1) {
                    return '全部用户'
                  }
                },
                icon: function(val) {
                  if (val === 4) {
                    return 'el-icon-mobile'
                  } else {
                    return false
                  }
                },
                handlerClick: this.handlerClick
              }

        ], // 表格的列数据
        handleSelectionChange: this.handleSelectionChange,//多选回调
        isSelection: false, // 表格有多选时设置
         selectable: function(val) {//禁用部分行多选（非必须）
          if (val.isVideoStatus === 1) {
            return false
          } else {
            return true
          }
        },
        isOperation: true, // 表格有操作列时设置,无操作列为false
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
          data: [
            {
              type: 'primary',
              label: '解封', // 操作名称
              permission: '8010201', // 后期这个操作的权限，用来控制权限
              handleRow: this.handleRow
            }
          ]
        }
      },
      msg: {},//储存搜索条件
      selected:[]//多选必须，储存已选内容
    }
  },
  created() {
    this.getList()
  },
  methods: {
    //获取表格数据
    // 获取列表数据
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
      if (this.msg.sortType) {
        data.sortType = this.msg.sortType
      }
      this.dataSource.loading = true
      list(data).then(res => {
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
     console.log(index,row,lable)
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

