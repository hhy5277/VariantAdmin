<template>
  <el-container class="field-props-container">
    <el-header class="field-props-header" v-if="!!!showingInDialog">[精度小数]字段属性设置</el-header>
    <el-main class="field-props-pane">
      <el-form ref="editorForm" :model="fieldProps" :rules="rules" label-position="left"
               label-width="220px" size="mini" @submit.native.prevent>
        <el-form-item label="字段名称" prop="name">
          <el-input v-model="fieldProps.name" :disabled="fieldState !== 1"></el-input>
        </el-form-item>
        <el-form-item label="显示名称" prop="label">
          <el-input v-model="fieldProps.label"></el-input>
        </el-form-item>
        <el-form-item label="小数精度位数（不超过6位）">
          <el-input-number v-model="fieldProps.fieldViewModel.precision"
                    :min="0" :max="6" style="width: 100%"></el-input-number>
        </el-form-item>
        <el-form-item label="最小值">
          <el-input v-model.number="fieldProps.fieldViewModel.minValue"
                           type="number" style="width: 100%"></el-input>
        </el-form-item>
        <el-form-item label="最大值">
          <el-input v-model.number="fieldProps.fieldViewModel.maxValue"
                           type="number" style="width: 100%"></el-input>
        </el-form-item>
        <el-form-item label="字段校验函数(可多选)" prop="fieldViewModel.validators">
          <el-select multiple allow-create filterable default-first-option :popper-append-to-body="false"
                     v-model="fieldProps.fieldViewModel.validators" style="width: 100%">
            <el-option
                    v-for="(vt, vtIdx) in validators"
                    :key="vtIdx"
                    :label="vt.label"
                    :value="vt.value">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="是否在列表中默认显示">
          <el-radio-group v-model="fieldProps.defaultMemberOfListFlag" style="float: right">
            <el-radio :label="true">是</el-radio>
            <el-radio :label="false">否</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="是否允许空值">
          <el-radio-group v-model="fieldProps.nullable" style="float: right">
            <el-radio :label="true">是</el-radio>
            <el-radio :label="false">否</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="新建记录时允许修改字段">
          <el-radio-group v-model="fieldProps.creatable" style="float: right">
            <el-radio :label="true">是</el-radio>
            <el-radio :label="false">否</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="更新记录时允许修改字段">
          <el-radio-group v-model="fieldProps.updatable" style="float: right">
            <el-radio :label="true">是</el-radio>
            <el-radio :label="false">否</el-radio>
          </el-radio-group>
        </el-form-item>
        <hr style="border: 0;border-top: 1px dotted #cccccc" />
        <el-form-item>
          <el-button type="primary" size="medium" style="width: 120px" @click="saveField">保存字段</el-button>
          <el-button size="medium" v-if="!!showingInDialog" @click="cancelSave">取消</el-button>
        </el-form-item>
      </el-form>
    </el-main>
  </el-container>
</template>

<script>
  import { addField } from '@/api/system-manager'
  import FieldState from "@/views/system/field-state-variables";
  import {fieldEditorMixin} from "@/views/system/field-editor/field-editor-mixin";

  export default {
    name: "DecimalWidgetEditor",
    props: {
      entity: String,
      fieldName: String,
      showingInDialog: Boolean,
      fieldState: {
        type: Number,
        default: FieldState.NEW,
      }
    },
    mixins: [fieldEditorMixin],
    data() {
      return {
        fieldProps: {
          'name': '',
          'label': '',
          'type': 'Decimal',
          'defaultMemberOfListFlag': true,
          'nullable': false,
          'creatable': true,
          'updatable': true,
          'fieldViewModel': {
            'minValue': -999999999,
            'maxValue': 999999999,
            'precision': 2,
            'validators': [],
          },
        },

        rules: {
          name: [
            {required: true, message: '请输入字段名称', trigger: 'blur'},
            {pattern: /^[A-Za-z\d]+$/, message: '请以英文字母、数字开头，不能包含中文字符', trigger: 'blur'},
            {min: 2, max: 30, message: '请输入至少两个字符', trigger: 'blur'},
          ],
          label: [
            {required: true, message: '请输入显示名称', trigger: 'blur'},
            {pattern: /^[A-Za-z\d\u4e00-\u9fa5]+[_-]*/, message: '请以中文、英文字母、数字开头，中间可输入下划线或横杠',
              trigger: 'blur'},
            {min: 2, max: 30, message: '请输入至少两个字符', trigger: 'blur'},
          ],
        },

        validators: [],

      }
    },
    mounted() {
      //mixin
    },
    methods: {
      saveField() {
        let validResult = true
        this.$refs['editorForm'].validate((success) => {
          if (!success) {
            validResult = false
            return false
          }
        })
        if (!validResult) return

        this.fieldProps.type = 'Decimal'
        if (this.fieldState === FieldState.NEW) {
          this.createNewField()
        } else if (this.fieldState === FieldState.EDIT) {
          this.modifyOldField()
        } else {
          // error
        }
      },

      cancelSave() {
        this.$emit('cancelSave')
      },
    }
  }
</script>

<style lang="scss" scoped>
  @import "@/styles/form-layout/field-editor-common.scss";
</style>
