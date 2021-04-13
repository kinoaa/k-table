<template>
  <div>
    <div class="filter-container">
      <el-date-picker
        v-if="filterData.timeSelect"
        v-model="dateRange"
        style="width: 300px"
        type="daterange"
        start-placeholder="开始日期"
        end-placeholder="结束日期"
        :default-time="['', '']"
        :picker-options="pickerOptions"
        class="filter-item"
      />
      <template v-if="filterData.elinput">
        <el-input
          v-for="(item,index) in filterData.elinput"
          :key="index"
          v-model="listQuery[item.key]"
          :placeholder="item.name"
          :style="{'width':item.width?item.width+'px':'200px'}"
          class="filter-item"
        />
      </template>
      <template v-if="filterData.elselect">
        <el-select
          v-for="(item,index) in filterData.elselect"
          :key="index"
          v-model="listQuery[item.key]"
          :placeholder="item.name"
          clearable
          :style="{'width':item.width?item.width+'px':'90px'}"
          class="filter-item"
        >
          <el-option
            v-for="i in item.option"
            :key="i.key"
            :label="i.value"
            :value="i.value"
          />
        </el-select>
      </template>
      <div class="btn">
        <el-button class="filter-item" type="primary" @click="handleSearch">
          搜索
        </el-button>
        <el-button class="filter-item" type="warning" @click="handleRest">
          重置
        </el-button>
      </div>
    </div>
  </div>
</template>

<script>
// 搜索栏组件
// filterData:{
//   timeSelect:true,
//   elinput:[
//     {
//       name:'姓名',
//       key:'userName'
//     }
//   ],
//   elselect:[
//     {
//       name:'部门',
//       key:'department'
//       option:[{
//         key:1,
//         value:'技术部'
//       }]
//     }
//   ]
// }
export default {
  props: {
    // eslint-disable-next-line vue/require-default-prop
    filterData: {
      type: Object
    }
  },
  data() {
    return {
      pickerOptions: {
        disabledDate(time) {
          return time.getTime() > Date.now()
        }
      },
      dateRange: ['', ''],
      listQuery: {}
    }
  },
  watch: {
    'filterData'(val) {
      console.log(val)
      if (val.elinput.length > 0) {
        val.elinput.map(item => {
          this.listQuery[item.key] = ''
        })
      }
      if (val.elselect.length > 0) {
        val.elinput.map(item => {
          this.listQuery[item.key] = ''
        })
      }
    },
    'filterData.rest': {
      handler: function(val) {
        if (val) {
          this.handleRest()
        }
      },
      deep: true
    }
  },
  methods: {
    handleSearch() {
      console.log('搜索成功', this.listQuery)
      const data = this.$global.deepClone(this.listQuery)
      if (this.dateRange && this.dateRange[0] !== '') {
        const startTime = this.$moment(this.dateRange[0]).format('YYYY-MM-DD') + ' 00:00:00'
        const endTime = this.$moment(this.dateRange[1]).format('YYYY-MM-DD') + ' 23:59:59'
        data.beginDate = startTime
        data.endDate = endTime
      }
      Object.keys(data).forEach(function(key) {
        if (data[key] === '') {
          delete data[key]
        }
      })
      this.$emit('filterMsg', data)
    },
    handleRest() {
      const data = this.$global.deepClone(this.listQuery)
      Object.keys(data).forEach(function(key) {
        data[key] = ''
      })
      this.listQuery = data
      this.dateRange = ['', '']
      console.log('重置成功', this.listQuery)
    }
  }
}
</script>

<style  scoped lang='scss'>
.filter-item{
  margin-left: 10px;
  display: inline-block;
}
.filter-container .filter-item:nth-of-type(1){
  margin-left: 0px;
}
.btn{
  display: inline-block;
  margin-left: 10px;
}
</style>
