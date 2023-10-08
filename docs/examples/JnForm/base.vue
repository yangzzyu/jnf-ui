<!--
 * @Author: yangyu 1431330771@qq.com
 * @Date: 2023-09-19 11:45:27
 * @LastEditors: yangyu 1431330771@qq.com
 * @LastEditTime: 2023-10-07 17:26:52
 * @FilePath: \jnf-ui-master\docs\examples\JnForm\base.vue
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
<template>
  <t-layout-page>
    <t-layout-page-item>
      <jn-button @click="open">打开</jn-button>
      <el-dialog
        :title="'212123'"
        width="70%"
        v-model="dialogSelectEnt"
        draggable
        @close="handleClose"
      >
        {{ formData }}

        <jn-form
          ref="detailForm"
          class="baseForm"
          label-width="110px"
          :formOpts="formOptions"
          v-model="formData"
          :disabled="false"
        ></jn-form>
        <el-button @click="handleClose">取消</el-button>
        <el-button type="primary" @click="submitForm"> 提交 </el-button>
      </el-dialog>
    </t-layout-page-item>
  </t-layout-page>
</template>
<script setup lang="ts">
import { ref, onMounted, nextTick } from 'vue'
// import { formOptions } from './config'
const dialogSelectEnt = ref(false)
let detailForm = ref()
const formData = ref({ saleDeptCode: '1212121' })
// /<BaseForm.FormOptions[]>
const changeSaleOrg = (e, row) => {
  console.log(e, row, 'eqeqwwq')
  row.saleCountry = '31312321'
  row.saleGroup = e
  // formOptions.value.saleCountry.children = [
  //   {
  //     label: '前纺一车间',
  //     value: 'W1',
  //   },
  //   {
  //     label: '前纺二车间',
  //     value: 'W2',
  //   },
  //   {
  //     label: '前纺三车间',
  //     value: 'W3',
  //   },
  // ]
}
const sapOptions = ref([])
const remoteMethod = (query: string) => {
  if (query) {
    setTimeout(() => {
      sapOptions.value = [
        {
          label: '前纺一车间',
          value: 'W1',
        },
        {
          label: '前纺二车间',
          value: 'W2',
        },
        {
          label: '前纺三车间',
          value: 'W3',
        },
      ]
    }, 1000)
  } else {
    sapOptions.value = []
  }
}
const formOptions = ref({
  title1: {
    type: 'title',
    label: '基础数据信息',
    colSize: 24,
    attrs: {
      isShowExpand: true,
    },
  },
  saleOrg: {
    type: 'select',
    label: '销售组织',
    placeholder: '请输入销售组织',
    required: true,
    rules: [],
    colSize: 24,
    attrs: {
      filterable: true,
      remote: true,
      reservekeyword: true,
      remoteMethod: remoteMethod,
      clearable: true,
    },
    events: {
      change: ($event, val) => changeSaleOrg($event, val),
      // focus: ($event, row) => focus($event, row),
      // blur: ($event, row) => blur($event, row),
    },
    // dictType: 'customer_file_organization',
    children: sapOptions,
  },
  saleDeptCode: {
    type: 'select',
    label: '销售部门代码',
    placeholder: '请输入销售部门代码',
    required: true,
    rules: [],
    colSize: 24,
    attrs: {
      clearable: true,
      filterable: true,
      allowCreate: true,
      defaultFirstOption: true,
      props: {
        label: 'saleDeptCode',
        value: 'saleDeptCode',
      },
    },
    events: {
      change: null,
    },
    children: [],
  },
  saleDept: {
    type: 'input',
    label: '销售部门描述',
    placeholder: '请输入销售部门描述',
    required: true,
    rules: [],
    colSize: 24,
    attrs: (row) => {
      return {
        disabled:
          row.saleDeptCode == null || row.saleDeptCode == '' ? true : false,
        // colProp: 24,
      }
    },
  },
  saleGroupCode: {
    type: 'select',
    label: '销售组代码',
    placeholder: '请输入销售组代码',
    required: true,
    rules: [],
    colSize: 24,
    attrs: {
      clearable: true,
      filterable: true,
      allowCreate: true,
      defaultFirstOption: true,
      props: {
        label: 'saleGroupCode',
        value: 'saleGroupCode',
      },
    },
    events: {
      change: null,
    },
    children: [],
  },
  saleGroup: {
    type: 'input',
    label: '销售组描述',
    placeholder: '请输入销售组描述',
    required: true,
    rules: [],
    colSize: 24,
    attrs: {
      clearable: true,
    },
  },
  saleCountry: {
    type: 'select',
    label: '行政区划',
    placeholder: '请输入行政区划',
    // required: true,
    rules: [],
    colSize: 24,
    attrs: {
      clearable: true,
      props: {
        label: 'name',
        value: 'name',
      },
    },
    children: [],
  },
  title2: {
    type: 'title',
    label: '基础数据信息32333',
    colSize: 24,
    attrs: {
      isShowExpand: true,
    },
  },
  saleDeptCode1: {
    type: 'checkbox-group',
    label: '销售部门代码111',
    placeholder: '请输入销售部门代码',
    required: true,
    rules: [],
    colSize: 24,
    attrs: {
      clearable: true,
      filterable: true,
      allowCreate: true,
      defaultFirstOption: true,
      // props: {
      //   label: 'saleDeptCode',
      //   value: 'saleDeptCode',
      // },
    },
    events: {
      change: null,
    },
    children: [
      {
        label: '前纺一车间',
        value: 'W1',
      },
      {
        label: '前纺二车间',
        value: 'W2',
      },
      {
        label: '前纺三车间',
        value: 'W3',
      },
    ],
  },
  saleCountry22: {
    type: 'tree-select',
    label: '行政区划',
    placeholder: '请输入行政区划',
    // required: true,
    rules: [],
    colSize: 24,
    attrs: {
      clearable: true,
      data: [
        {
          value: '1',
          label: 'Level one 1',
          children: [
            {
              value: '1-1',
              label: 'Level two 1-1',
              children: [
                {
                  value: '1-1-1',
                  label: 'Level three 1-1-1',
                },
              ],
            },
          ],
        },
      ],
    },
  },
})

onMounted(() => {})
async function open() {
  dialogSelectEnt.value = true
  formData.value={saleDeptCode:'5555555555'}
  await nextTick()
  detailForm?.value.initForm({
    formData: formData.value,
  })
}

function handleClose() {
  console.log('9090909')
  detailForm.value.resetFields()
  dialogSelectEnt.value = false
}
function submitForm(params) {
  let validate = detailForm.value.validate()
  validate((valid: boolean) => {
    if (valid) {
    }
  })
  let model = detailForm.value.getFormData()
}
</script>
