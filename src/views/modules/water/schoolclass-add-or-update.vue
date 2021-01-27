<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="学校ID" prop="sysUserId">
          <el-input v-model="dataForm.sysUserId" placeholder="学校ID"></el-input>
      </el-form-item>
          <el-form-item label="年级" prop="schGrade">
          <el-input v-model="dataForm.schGrade" placeholder="年级"></el-input>
      </el-form-item>
          <el-form-item label="班级" prop="schClass">
          <el-input v-model="dataForm.schClass" placeholder="班级"></el-input>
      </el-form-item>
          <el-form-item label="班主任ID" prop="teacherId">
          <el-input v-model="dataForm.teacherId" placeholder="班主任ID"></el-input>
      </el-form-item>
          <el-form-item label="应缴人数" prop="needAmount">
          <el-input v-model="dataForm.needAmount" placeholder="应缴人数"></el-input>
      </el-form-item>
      </el-form>
    <template slot="footer">
      <el-button @click="visible = false">{{ $t('cancel') }}</el-button>
      <el-button type="primary" @click="dataFormSubmitHandle()">{{ $t('confirm') }}</el-button>
    </template>
  </el-dialog>
</template>

<script>
import debounce from 'lodash/debounce'
export default {
  data () {
    return {
      visible: false,
      dataForm: {
        id: '',
        sysUserId: '',
        schGrade: '',
        schClass: '',
        teacherId: '',
        needAmount: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        sysUserId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        schGrade: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        schClass: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        teacherId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        needAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    init () {
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.getInfo()
        }
      })
    },
    // 获取信息
    getInfo () {
      this.$http.get(`/water/schoolclass/${this.dataForm.id}`).then(({ data: res }) => {
        if (res.code !== 0) {
          return this.$message.error(res.msg)
        }
        this.dataForm = {
          ...this.dataForm,
          ...res.data
        }
      }).catch(() => {})
    },
    // 表单提交
    dataFormSubmitHandle: debounce(function () {
      this.$refs['dataForm'].validate((valid) => {
        if (!valid) {
          return false
        }
        this.$http[!this.dataForm.id ? 'post' : 'put']('/water/schoolclass/', this.dataForm).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 500,
            onClose: () => {
              this.visible = false
              this.$emit('refreshDataList')
            }
          })
        }).catch(() => {})
      })
    }, 1000, { 'leading': true, 'trailing': false })
  }
}
</script>
