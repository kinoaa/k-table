<template>
  <div>
    <div v-if="dataSource.tool" class="tool">
      <el-button
        v-for="(item) in dataSource.tool"
        :key="item.key"
        v-permission="item.permission"
        class="filter-item"
        :style="{'background':item.bgColor,borderColor:item.bgColor}"
        :type="item.type || 'primary'"
        @click="item.handleClick(item.name,$event)"
      >
        {{ item.name }}
      </el-button>
    </div>
    <el-table
      ref="table"
      v-loading="dataSource.loading"
      style="width: 100%;"
      :class="{ 'no-data': !dataSource.data || !dataSource.data.length }"
      :data="dataSource.data"
      @row-click="getRowData"
      @selection-change="dataSource.handleSelectionChange"
    >
      <!-- 是否有多选 -->
      <el-table-column
        v-if="dataSource.isSelection"
        :selectable="dataSource.selectable"
        type="selection"
        :width="dataSource.selectionWidth || 50"
        align="center"
      />
      <!-- 是否需要序号 -->
      <el-table-column
        v-if="dataSource.isIndex"
        type="index"
        label="序号"
        width="55"
        align="center"
      />

      <template v-for="item in dataSource.cols">
        <!-- 表格的列展示 特殊情况处理 比如要输入框 显示图片 -->
        <el-table-column
          v-if="item.isTemplate"
          :key="item.prop"
          v-bind="item"
        >
          <template slot-scope="scope">
            <!-- 比如要输入框 显示图片等等 自己定义 -->
            <slot :name="item.prop" :scope="scope" />
          </template>
        </el-table-column>
        <!-- 需要特殊颜色显示字体-->
        <el-table-column
          v-if="item.isSpecial"
          :key="item.prop"
          v-bind="item"
          align="center"
        >
          <template slot-scope="scope">
            <span :class="item.isSpecialClass(scope.row[scope.column.property])">{{ item.isSpecial(scope.row[scope.column.property]) }}</span>
          </template>
        </el-table-column>
        <!-- 需要带图标的某列，带回调事件-->
        <el-table-column
          v-if="item.isIcon"
          :key="item.prop"
          v-bind="item"
          align="center"
        >
          <template slot-scope="scope">
            <span>
              <span>{{ item.filter(scope.row[scope.column.property]) }}</span>
              <i v-if="item.icon" :class="[item.icon(scope.row[scope.column.property]),'icon-normal']" @click="item.handlerClick(scope.row)" />
            </span>
            <!-- 比如要输入框 显示图片等等 自己定义 -->
            <slot :name="item.prop" :scope="scope" />
          </template>
        </el-table-column>
        <!-- 图片带tooltip -->
        <el-table-column
          v-if="item.isImagePopover"
          :key="item.prop"
          v-bind="item"
          align="center"
        >
          <template slot-scope="scope">
            <el-popover
              placement="right"
              title=""
              trigger="hover"
            >
              <img class="image-popover" :src="scope.row[scope.column.property]+'?x-oss-process=image/quality,q_60'" alt="">
              <img slot="reference" class="reference-img" :src="scope.row[scope.column.property]+'?x-oss-process=image/quality,q_10'" alt="">
            </el-popover>
          </template>
        </el-table-column>
        <!-- 大部分适用 -->
        <el-table-column
          v-if="!item.isImagePopover && !item.isTemplate && !item.isSpecial&&!item.isIcon"
          :key="item.prop"
          v-bind="item.isCodeTableFormatter ? Object.assign({ formatter: item.isCodeTableFormatter }, item) : item"
          align="center"
          show-overflow-tooltip
        />
      </template>
      <!-- 是否有操作列 -->
      <!-- 没有数据时候不固定列 -->
      <el-table-column
        v-if="dataSource.isOperation"
        :show-overflow-tooltip="dataSource.operation.overflowTooltip"
        v-bind="dataSource.data && dataSource.data.length ? { fixed: 'right' } : null"
        style="margin-right:20px"
        class-name="handle-td"
        label-class-name="tc"
        :width="dataSource.operation.width"
        :label="dataSource.operation.label"
        align="center"
      >
        <!-- UI统一一排放3个，4个以上出现更多 -->
        <template slot-scope="scope">
          <!-- 三个一排的情况，去掉隐藏的按钮后的长度 -->
          <template v-if="dataSource.operation.data.length > 0">
            <div class="btn">
              <div v-for="(item) in dataSource.operation.data" :key="item.label">
                <template v-if="item.type!=='icon'">
                  <el-button
                    v-permission="item.permission"
                    v-bind="item"
                    :type="item.type?item.type:''"
                    size="mini"
                    @click.native.prevent="item.handleRow(scope.$index, scope.row, item.label)"
                  >
                    {{ item.label }}
                  </el-button>
                </template>
                <template v-else>
                  <i :class="[icon,item.icon]" v-bind="item" @click="item.handleRow(scope.$index, scope.row, item.label)" />
                </template>
              </div>
            </div>
          </template>
        </template>
      </el-table-column>
    </el-table>
    <div class="page">
      <el-pagination
        v-if="dataSource.pageData.total>0"
        :current-page="dataSource.pageData.pageNum"
        :page-sizes="dataSource.pageData.pageSizes?dataSource.pageData.pageSizes:[5,10,15,20]"
        :page-size="dataSource.pageData.pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="dataSource.pageData.total"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
    </div>
  </div>
</template>

<script>
//  dataSource: {
//          tool:[
//            {
//              name: '新增用户', //按钮名称
//              key: 1,  // 唯一标识符
//              permission: 2010106, // 权限点
//              type: '',  // 使用element自带按钮类型
//              bgColor: '#67c23a', // 自定义背景色
//              handleClick: this.handleAdd //自定义事件
//            },
//          ]
//         data: [], // 表格数据
//         cols: [], // 表格的列数据
//         handleSelectionChange:(val)=>{} //点击行选中多选返回选中数组
//         isSelection: false, // 表格有多选时设置
//         isOperation: true, // 表格有操作列时设置
//         isIndex: true, // 列表序号
//         loading: true, // loading
//         pageData: {
//          total: 0, // 总条数
//          pageSize: 10, // 每页数量
//          pageNum: 1, // 页码
//          pageSize:[5,10,15,20]// 每页数量
//         }
//         operation: {
//           // 表格有操作列时设置
//           label: '操作', // 列名
//           width: '350', // 根据实际情况给宽度
//           data: [
//             {
//               label: '冻结', // 操作名称
//               permission:'' //权限点
//               type: 'info', //按钮类型
//               handleRow: function(){} // 自定义事件
//             },
//           ]
//         }
//       },
export default {
  // 接收父组件传递过来的值
  props: {
    //  表格数据和表格部分属性的对象
    // eslint-disable-next-line vue/require-default-prop
    dataSource: {
      type: Object
    }
  },
  data() {
    return {
      // currentPage4: 4
    }
  },
  watch: {
    'dataSource.cols': { // 监听表格列变化
      deep: true,
      handler() {
        // 解决表格列变动的抖动问题
        this.$nextTick(this.$refs.table.doLayout)
      }
    }
  },
  methods: {
    handleAdd(name) {
      console.log(name)
      this.$emit('toolMsg', name)
    },
    handleRow(index, row, lable) {
      console.log(index, row, lable)
    },
    handleSizeChange(val) {
      this.$emit('changeSize', val)
      console.log(`每页 ${val} 条`)
    },
    handleCurrentChange(val) {
      this.$emit('changeNum', val)
      console.log(`当前页: ${val}`)
    },
    // 点击行即可选中
    getRowData(row) {
      this.$refs.table.toggleRowSelection(row)
    }
  }
}
</script>
<style lang="scss" scoped>
.page{
  margin-top: 20px;
}
.btn{
  display: flex;
  justify-content: center;
}
.btn div{
  margin-left: 5px;
}
.reference-img{
  width: 40px;
  height: 40px;
  background-size:100% 100%;
  border-radius: 4px;
}
.image-popover{
  width: 200px;
  height: 200px;
  background-size:100% 100%;
}
.icon {
  width: 25px;
  font-size: 20px;
  font-weight: bold;
}
</style>
