<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="学期ID" prop="termId">
          <el-input v-model="dataForm.termId" placeholder="学期ID"></el-input>
      </el-form-item>
          <el-form-item label="学期名称，如2020年上学期" prop="termName">
          <el-input v-model="dataForm.termName" placeholder="学期名称，如2020年上学期"></el-input>
      </el-form-item>
          <el-form-item label="缴费开始时间" prop="beginDate">
          <el-input v-model="dataForm.beginDate" placeholder="缴费开始时间"></el-input>
      </el-form-item>
          <el-form-item label="缴费结束时间" prop="endDate">
          <el-input v-model="dataForm.endDate" placeholder="缴费结束时间"></el-input>
      </el-form-item>
          <el-form-item label="学校ID,即sys_user_id" prop="schoolId">
          <el-input v-model="dataForm.schoolId" placeholder="学校ID,即sys_user_id"></el-input>
      </el-form-item>
          <el-form-item label="学校名称" prop="schoolName">
          <el-input v-model="dataForm.schoolName" placeholder="学校名称"></el-input>
      </el-form-item>
          <el-form-item label="年级名称" prop="gradeName">
          <el-input v-model="dataForm.gradeName" placeholder="年级名称"></el-input>
      </el-form-item>
          <el-form-item label="班级名称" prop="className">
          <el-input v-model="dataForm.className" placeholder="班级名称"></el-input>
      </el-form-item>
          <el-form-item label="班级ID" prop="classId">
          <el-input v-model="dataForm.classId" placeholder="班级ID"></el-input>
      </el-form-item>
          <el-form-item label="应缴人数" prop="needAmount">
          <el-input v-model="dataForm.needAmount" placeholder="应缴人数"></el-input>
      </el-form-item>
          <el-form-item label="实缴人数" prop="actualAmount">
          <el-input v-model="dataForm.actualAmount" placeholder="实缴人数"></el-input>
      </el-form-item>
          <el-form-item label="班主任ID，即sys_user_id" prop="teacherId">
          <el-input v-model="dataForm.teacherId" placeholder="班主任ID，即sys_user_id"></el-input>
      </el-form-item>
          <el-form-item label="班主任姓名" prop="teacherName">
          <el-input v-model="dataForm.teacherName" placeholder="班主任姓名"></el-input>
      </el-form-item>
          <el-form-item label="二维码地址" prop="qrUrl">
          <el-input v-model="dataForm.qrUrl" placeholder="二维码地址"></el-input>
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
        termId: '',
        termName: '',
        beginDate: '',
        endDate: '',
        schoolId: '',
        schoolName: '',
        gradeName: '',
        className: '',
        classId: '',
        needAmount: '',
        actualAmount: '',
        teacherId: '',
        teacherName: '',
        qrUrl: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        termId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        termName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        beginDate: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        endDate: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        schoolId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        schoolName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        gradeName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        className: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        classId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        needAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        actualAmount: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        teacherId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        teacherName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        qrUrl: [
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
      this.$http.get(`/water/schooltermdetail/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/water/schooltermdetail/', this.dataForm).then(({ data: res }) => {
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
