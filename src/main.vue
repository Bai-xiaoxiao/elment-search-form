<template>
  <el-form ref="editFrm" inline :label-width="labelWidth" @submit.native.prevent @keyup.enter.native="search">
    <el-form-item v-for="item in config" :key="item.label" :label="item.label | lableFilter" style="margin-right: 20px;" >
      <!-- 自定义slot -->
      <slot v-if="item.slot" :name="item.slot" :slotData="value"></slot>
      <!-- 普通输入框 -->
      <el-input
        v-else-if="item.type == 'input'"
        v-model.trim="queryParams[item.model]"
        clearable
        :placeholder="item.placeholder || `请输入${item.label}`"
      ></el-input>
      <!-- select -->
      <el-select
        v-else-if="item.type == 'select'"
        v-model="queryParams[item.model]"
        clearable
        :filterable="item.filterable"
        :placeholder="item.placeholder || `请选择${item.label}`"
      >
        <el-option
          v-for="o in item.options"
          :key="o.value"
          :label="o.label"
          :value="o.value"
        ></el-option>
      </el-select>
      <!-- 日期选择 -->
      <el-date-picker
        v-else-if="item.type == 'date-picker'"
        v-model="queryParams[item.model]"
        :type="item.dateType || 'datetimerange'"
        :value-format="item.valueFormat || 'yyyy-MM-dd HH:mm:ss'"
        :picker-options="item.pickerOptions || pickerOptions"
        :default-time="['00:00:00', '23:59:59']"
        range-separator="至"
        start-placeholder="开始日期"
        end-placeholder="结束日期"
        align="right"
      >
      </el-date-picker>
      <!-- 时间段选择 -->
      <el-time-picker
        v-else-if="item.type == 'time-picker'"
        is-range
        v-model="queryParams[item.model]"
        value-format="HH:mm:ss"
        :default-value="['2020-01-01 00:00:00', '2020-01-01 23:59:59']"
        range-separator="至"
        start-placeholder="开始时间"
        end-placeholder="结束时间"
        placeholder="选择时间范围"
      >
      </el-time-picker>
    </el-form-item>
    <el-form-item>
      <el-button @click="search" type="primary">
        查询
      </el-button>
      <el-button @click="reset">
        重置
      </el-button>
    </el-form-item>
  </el-form>
</template>

<script>
import { pickerOptions } from './contract';
const setDefaultValue = (value) => {
  if(value === undefined || value === null || isNaN(value)) {
    return ''
  }
  return value
}

export default {
  name: 'SearchForm',
  props: {
    value: {
      type: Object,
      required: true
    },
    config: {
      type: Array,
    },
    labelWidth: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      queryParams: {},
      pickerOptions,
    };
  },
  watch: {
    config: {
      immediate: true,
      handler(newValue, oldValue) {
        newValue.forEach((item) => {
          if(this.queryParams[item.model] === undefined && item.model) {
            this.$set(this.queryParams, item.model, setDefaultValue(this.value[item.model]))
          }
        });
        this.$emit('input', Object.assign(this.value, this.queryParams))
      },
    },
    queryParams: {
      deep: true,
      handler(newVal) {
        this.$emit('input', Object.assign(this.value, newVal))
      }
    },
    value: {
      deep: true,
      handler(newVal) {
        const {queryParams} = this;
        for (const key in newVal) {
          const item = newVal[key];
          if(queryParams.hasOwnProperty(key)) {
            queryParams[key] = item;
          }
        }
      }
    }
  },
  methods: {
    search() {
      this.$emit('search');
    },
    reset() {
      let o = this.queryParams;
      Object.keys(o).forEach(key => {
        o[key] = ''
      })
      this.$nextTick(() => {
        this.$emit('search');
      });
    }
  },
  filters: {
    lableFilter(value) {
      if(value) return value + '：';
      return undefined
    }
  }
};
</script>

<style lang="scss">
/* .el-date-editor .el-range-separator{
  width: auto;
} */
</style>