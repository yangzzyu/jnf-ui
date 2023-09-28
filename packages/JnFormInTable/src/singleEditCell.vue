<template>
  <div class="single_edit_cell">
    <!-- 编辑组件自定义插槽 -->
    <template v-if="configEdit.editSlotName">
      <slot />
    </template>
    <template v-if="!configEdit?.hasOwnProperty('children')">
      <component
        v-if="configEdit.type !== 'upload' && configEdit.type !== 'editor'"
        v-bind="cAttrs(configEdit)"
        :is="`el-${configEdit.type}`"
        v-model="scope.row[prop]"
        :placeholder="configEdit.placeholder"
        v-on="cEvent(configEdit)"
      >
        <!-- 前置文本 -->
        <template #prepend v-if="configEdit.prepend">{{
          configEdit.prepend
        }}</template>
        <!-- 后置文本 -->
        <template #append v-if="configEdit.append">{{
          configEdit.append
        }}</template>
      </component>
      <!-- <el-upload
        v-if="configEdit.type === 'upload'"
        v-bind="configEdit.uploadAttrs"
        :on-preview="onPreview"
        :on-remove="onRemove"
        :on-success="onSuccess"
        :on-error="onError"
        :on-progress="onProgress"
        :on-change="onChange"
        :before-upload="beforeUpload"
        :before-remove="beforeRemove"
        :http-request="httpRequest"
        :on-exceed="onExceed"
        class="m-el-upload"
      >
        <slot name="uploadArea"></slot>
        <slot name="uploadTip"></slot>
      </el-upload> -->
    </template>
    <component
      v-else
      :is="`el-${configEdit.type}`"
      v-model="scope.row[prop]"
      :placeholder="configEdit.placeholder"
      :disabled="configEdit.attrs?.disabled ?? props?.disabled"
      v-bind="cAttrs(configEdit)"
      v-on="cEvent(configEdit)"
    >
      <component
        v-if="compChildValue(configEdit)"
        :is="`el-${compChildValue(configEdit)}`"
        v-for="(child, i) in configEdit?.children"
        :key="i"
        :label="child.label ?? child[configEdit.attrs?.props.label]"
        :value="child.value ?? child[configEdit.attrs?.props.value]"
        :disabled="child?.disabled ?? props?.disabled"
      >
      </component>
    </component>
  </div>
</template>
<script lang="ts">
export default {
  name: 'SingleEditCell',
}
</script>
<script setup lang="ts">
import { computed, ref, watch } from 'vue'
const props = defineProps({
  /** 编辑配置项说明
   * label: '爱好', // placeholder显示
   * editComponent: 'el-select', // 组件
   * type: 'select-arr', // option显示
   * list: 'hobbyList', // 下拉选择数据源
   * arrLabel: 'label', // 下拉选择中文显示
   * arrKey: 'value' // 下拉选择number显示（最终传后台）
   * bind:{} // 组件衍生属性（即第三方组件属性）
   */
  configEdit: {
    type: Object,
    default: () => ({}),
  },
  formData: {
    type: Object,
    default: () => ({}),
  },
  scope: {
    type: Object,
    default: {},
  },
  prop: {
    type: String,
    default: '',
  },
  disabled: {},
  // modelValue: {
  //   type: [String, Number, Array, Boolean],
  // },
})
// 抛出事件
const emits = defineEmits(['handleEvent', 'update:modelValue', 'keyupHandle'])
// 键盘事件
const keyUpHandle = ($event) => {
  emits('keyupHandle', $event, props.scope.$index, props.prop)
}
// 引用第三方事件
const cEvent = computed(() => {
  return (configEdit: any) => {
    let event = { ...configEdit.events }
    let changeEvent = {}
    Object.keys(event).forEach((v) => {
      changeEvent[v] = (e) => {
        if (e) {
          event[v] && event[v](e, props.scope.row,props.formData)
        } else {
          event[v] && event[v](props.scope.row,props.formData)
        }
      }
    })
    return { ...changeEvent }
  }
})
// 参数配置
const cAttrs = computed(() => {
  return (item: any) => {
    const itemAttrs =
      typeof item.attrs == 'function'
        ? {
            ...item.attrs({ row: props.scope.row, formData: props.formData }),
            disabled: props?.disabled
              ? true
              : item.attrs({ row: props.scope.row, formData: props.formData })
                  ?.disabled,
          }
        : {
            clearable: true,
            filterable: true,
            disabled: item.attrs?.disabled ?? props?.disabled,
            // ...$attrs,
            ...item.attrs,
          }
    return itemAttrs
  }
})
// 子子组件value
const compChildValue = computed(() => {
  return (opt: any) => {
    switch (opt.type) {
      case 'select':
        return 'option'
      case 'checkbox-group':
        return 'checkbox'
      case 'radio-group':
        return 'radio'
      default:
        return null
    }
  }
})
// 子子组件label
const compChildLabel = computed(() => {
  return (configEdit: any, value) => {
    switch (configEdit.type) {
      case 'radio':
      case 'checkbox':
        return value.value
      case 'el-select-multiple':
      case 'select-arr':
        return value[configEdit.arrLabel || 'dictLabel']
      case 'select-obj':
        return value
    }
  }
})
// 子子组件文字展示
const compChildShowLabel = computed(() => {
  return (configEdit: any, value) => {
    switch (configEdit.type) {
      case 'radio':
      case 'checkbox':
        return value.label
      case 'el-select-multiple':
      case 'select-arr':
        return value[configEdit.arrLabel || 'dictLabel']
      case 'select-obj':
        return value
    }
  }
})
// placeholder的显示
const getPlaceholder = (row: any) => {
  let placeholder
  if (row.comp && typeof row.comp == 'string') {
    if (row.comp.includes('input')) {
      placeholder = '请输入' + row.label
    } else if (row.comp.includes('select') || row.comp.includes('date')) {
      placeholder = '请选择' + row.label
    } else {
      placeholder = row.label
    }
  }
  return placeholder
}
// 绑定的相关事件
const handleEvent = (type, val, editCom) => {
  // console.log('组件', type, val, editCom)
  emits('handleEvent', { type, val })
}
</script>
<style lang="scss">
.single_edit_cell {
  cursor: pointer;
}
</style>
