<script setup lang="ts">
import _ from 'lodash'
import { onMounted, Ref, ref } from 'vue'
type skuType = {
  color: string
  rule: string
  size: string
}
type rowType = {
  [index: string]: string | boolean
}

let radioValue: Ref<string[]> = ref([])
let specification = [
  {
    label: '颜色',
    key: 'color',
    value: ['白色', '黑色', '红色']
  },
  {
    label: '尺码',
    key: 'rule',
    value: ['110', '120', '130']
  },
  {
    label: '大小',
    key: 'size',
    value: ['大', '中', '小']
  }
]
let skus = [
  {
    color: '白色',
    rule: '120',
    size: '中'
  },
  {
    color: '白色',
    rule: '110',
    size: '小'
  },
  {
    color: '黑色',
    rule: '110',
    size: '小'
  },
  {
    color: '黑色',
    rule: '120',
    size: '小'
  },
  {
    color: '黑色',
    rule: '130',
    size: '大'
  },
  {
    color: '红色',
    rule: '130',
    size: '中'
  },
  {
    color: '红色',
    rule: '120',
    size: '中'
  },
  {
    color: '红色',
    rule: '120',
    size: '大'
  }
]
let decision = {} // 选中数据
let results: Ref<rowType> = ref({}) // 数据集
const select = (key: string, index: number, value: string) => {
  if (radioValue.value[index] === value) {
    radioValue.value[index] = ''
    decision = _.omit(decision, [key])
  } else {
    radioValue.value[index] = value
    _.assign(decision, { [key]: value })
  }
  const rowState: rowType = {}
  // 判断同列按钮显示隐藏
  _.keys(decision).map((item: string) => {
    const newDecision = _.omit(_.cloneDeep(decision), [item])
    const rowData = _.filter(skus, newDecision) as skuType[]
    rowData.map((val: rowType) => {
      rowState[item + val[item]] = true
    })
  })
  // 生成新的数据集合
  const newResults = _.reduce(
    _.filter(skus, decision) as skuType[],
    (sum, val) => {
      return _.defaults(
        _.mapKeys(val, (v, k) => k + v),
        sum
      )
    },
    {}
  )
  results.value = _.assign(newResults, rowState)
}
// 初始化数据集合
onMounted(() => {
  radioValue.value = []
  results.value = _.reduce(
    skus,
    (sum, val) => {
      return _.defaults(
        _.mapKeys(val, (v, k) => k + v),
        sum
      )
    },
    {}
  )
})
</script>

<template>
  <div style="width: 500px; margin: 0 auto">
    <div class="box">
      <div class="item" v-for="(item, index) in specification" :key="index" style="padding-top: 10px">
        <span>{{ item.label }}: </span>
        <a-radio-group button-style="solid" :value="radioValue[index]" style="margin-left: 10px">
          <template v-for="child in item.value">
            <a-radio-button
              :value="child"
              :name="item.key"
              :disabled="!results[item.key + child]"
              @click="select(item.key, index, child)"
            >
              {{ child }}
            </a-radio-button>
          </template>
        </a-radio-group>
      </div>
    </div>
    <div>sku:</div>
    <div v-for="item in skus">{{ item }}</div>
  </div>
</template>

<style scoped></style>
