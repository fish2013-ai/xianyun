<template>
  <div class="filters">
    <el-row type="flex" class="filters-top" justify="space-between" align="middle">
      <el-col :span="8">
        单程：
        {{data.info.departCity}} - {{data.info.destCity}}
        /
        {{data.info.departDate}}
      </el-col>
      <el-col :span="4">
        <el-select size="mini" v-model="airport" placeholder="起飞机场" >
        <!-- @change="handleAirport" -->
          <el-option
            v-for="(item, index) in data.options.airport"
            :key="index"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
      </el-col>
      <el-col :span="4">
        <el-select size="mini" v-model="flightTimes" placeholder="起飞时间" >
          <!-- @change="handleFlightTimes" -->
          <el-option
            v-for="(item, index) in data.options.flightTimes"
            :key="index"
            :label="`${item.from}:00 - ${item.to}:00`"
            :value="`${item.from},${item.to}`"
          ></el-option>
        </el-select>
      </el-col>
      <el-col :span="4">
        <el-select size="mini" v-model="company" placeholder="航空公司" >
          <!-- @change="handleCompany" -->
          <el-option
            v-for="(item, index) in data.options.company"
            :key="index"
            :label="item"
            :value="item"
          ></el-option>
        </el-select>
      </el-col>
      <el-col :span="4">
        <el-select size="mini" v-model="airSize" placeholder="机型" >
          <!-- @change="handleAirSize" -->
          <el-option
            v-for="(item, index) in sizeOptions"
            :key="index"
            :label="item.name"
            :value="item.size"
          ></el-option>
        </el-select>
      </el-col>
    </el-row>
    <div class="filter-cancel">
      筛选：
      <el-button type="primary" round plain size="mini" @click="handleFiltersCancel">撤销</el-button>
    </div>
    <!-- 调用filterData -->
    <span>{{filterData}}</span>
  </div>
</template>

<script>
export default {
  props: {
    data: {
      type: Object,
      default: {}
    }
  },
  data() {
    return {
      airport: "", // 机场
      flightTimes: "", // 出发时间
      company: "", // 航空公司
      airSize: "", // 机型大小
      sizeOptions: [
        { name: "大", size: "L" },
        { name: "中", size: "M" },
        { name: "小", size: "S" }
      ]
    };
  },
  computed: {
    // 多选：1.通过计算属性监听选项的变化，
    //  2. 默认每条数据都是符合条件
    // 3.过滤掉不符合选项的值，然后将符合条件的数组返回给父组件
    filterData() {
      const arr = this.data.flights.filter(item => {
        // 2. 默认每条数据都是符合条件
        let valid = true;
        // 过滤起飞机场
        if (this.airport && item.org_airport_name !== this.airport) {
          valid = false;
        }
        // 过滤起飞时间
        if (this.flightTimes) {
          const [from, to] = this.flightTimes.split(",");
          const start = +item.dep_time.split(":")[0];
          if (start < from || start >= to) {
            valid = false;
          }
        }
        // 过滤航空公司
        if (this.company && item.airline_name !== this.company) {
          valid = false;
        }
        // 过滤机型
        if (this.airSize && item.plane_size !== this.airSize) {
          valid = false;
        }
        return valid
      });
       // 触发父组件的修改dataList的函数
      this.$emit("setDataList", arr);
      return ""
    }
  },
  methods: {
    // // 选择机场时候触发
    // handleAirport(value) {
    //   const arr = this.data.flights.filter(val => {
    //     return value === val.org_airport_name;
    //   });
    //   // 触发父组件的修改dataList的函数
    //   this.$emit("setDataList", arr);
    // },
    // // 选择出发时间时候触发
    // handleFlightTimes(value) {
    //   const [from, to] = value.split(",");
    //   const arr = this.data.flights.filter(val => {
    //     const start = +val.dep_time.split(":")[0];
    //     return start >= from && start < to;
    //   });
    //   this.$emit("setDataList", arr);
    // },

    // // 选择航空公司时候触发
    // handleCompany(value) {
    //   const arr = this.data.flights.filter(val => {
    //     return value === val.airline_name;
    //   });
    //   // 触发父组件的修改dataList的函数
    //   this.$emit("setDataList", arr);
    // },
    // // 选择机型时候触发
    // handleAirSize(value) {
    //   // 过滤后数组
    //   const arr = this.data.flights.filter(val => {
    //     return value === val.plane_size;
    //   });
    //   // 触发父组件的修改dataList的函数
    //   this.$emit("setDataList", arr);
    // },
    // 撤销条件时候触发
    handleFiltersCancel() {
      this.airport = "";
      this.flightTimes = "";
      this.company = "";
      this.airSize = "";
      // this.$emit("setDataList", this.data.flights);
    }
  }
};
</script>

<style scoped lang="less">
.filters {
  margin-bottom: 20px;
}

.filters-top {
  > div {
    /deep/ .el-select {
      margin-left: 10px;
    }
  }
}

.filter-cancel {
  margin-top: 10px;
}
</style>