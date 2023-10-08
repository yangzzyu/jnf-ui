<template>
  <div class="subList" ref="subListRef">
    <jn-form
      ref="searchFormRef"
      isSearch
      :rowsTotal="props.rowsTotal"
      :formOpts="props.searchParamet"
      >
      <!-- :inline="true" -->
      <template #searchAction="scope">
        <jn-button
          type="primary"
          icon="Search"
          @click="$emit('search', { ...scope.model, current: 1 })"
          >查询</jn-button
        >
        <jn-button icon="Refresh" @click="resetSearchForm">重置</jn-button>
        <jn-button
          type="primary"
          v-if="!scope.isShowExpand"
          link
          @click="changeExpand"
        >
          <el-icon>
            <ArrowDown v-if="!subListOpenFlag" />
            <ArrowUp v-else />
          </el-icon>
          {{ !subListOpenFlag ? '展开' : '收起' }}</jn-button
        >
      </template>
    </jn-form>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'

let props = defineProps({
  rowsTotal: {
    type: Number,
    default: 999999,
  },
  searchParamet: {
    type: Object,
    default: () => {},
  },
})
const $emit = defineEmits([
  /**
   * 搜索
   * @arg {object} params - {current?: number}
   */
  'search',
])
// interface SubListItem {
//   id: number;
//   name: string;
// }
const searchFormRef = ref()

const subListHeight = ref<String>('')
const subListOpenFlag = ref(false)
const showSubListBtnFlag = ref(false)
const subListRef = ref()
const changeExpand = () => {
  subListOpenFlag.value = !subListOpenFlag.value
  searchFormRef.value.calculateShowCol(subListOpenFlag.value)
}

/** 重置 */
const resetSearchForm = () => {
  searchFormRef.value.resetFields()
  $emit('search', {
    current: 1,
  })
}
onMounted(() => {
  searchFormRef?.value.initForm({
    schema: props.searchParamet,
    formData: {},
  })
})

// 单纯重置表单
const resetSearchFormParams = () => {
  searchFormRef.value.resetFields()
}

// 暴露重置方法
defineExpose({
  resetSearchForm,
  resetSearchFormParams,
})
</script>
<style lang="scss" scoped>
$mainColor: #5864ff;

.search-btn {
  margin-left: 10px;
}

.btn {
  font-size: 14px;
  color: $mainColor;
  position: absolute;
  right: 0px;
  bottom: 2px;
  background-color: #fff;
  padding-left: 60px;
  padding-right: 10px;

  &:hover {
    color: orange;
  }
}

.subList {
  // background-color: #f5f6fa;
  display: flex;
  flex-wrap: wrap;
  position: relative;
  // overflow: hidden;

  li {
    margin: 15px;
    white-space: nowrap;

    &:hover {
      color: $mainColor;
    }
  }

  .active {
    color: $mainColor;
  }

  .btn {
    background-color: #f5f6fa;
    position: absolute;
    right: -20px;
    bottom: -5px;
    padding: 0px 20px;
    cursor: pointer;
    list-style-type: none;

    &:hover {
      color: orange;
    }
  }
}
</style>
