<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
          <el-form-item label="学期详情表ID" prop="detailId">
          <el-input v-model="dataForm.detailId" placeholder="学期详情表ID"></el-input>
      </el-form-item>
          <el-form-item label="学期ID" prop="termId">
          <el-input v-model="dataForm.termId" placeholder="学期ID"></el-input>
      </el-form-item>
          <el-form-item label="学期名称，如2020年上学期" prop="termName">
          <el-input v-model="dataForm.termName" placeholder="学期名称，如2020年上学期"></el-input>
      </el-form-item>
          <el-form-item label="学生姓名" prop="studentName">
          <el-input v-model="dataForm.studentName" placeholder="学生姓名"></el-input>
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
          <el-form-item label="手机号码" prop="phone">
          <el-input v-model="dataForm.phone" placeholder="手机号码"></el-input>
      </el-form-item>
          <el-form-item label="订单编号" prop="orderNo">
          <el-input v-model="dataForm.orderNo" placeholder="订单编号"></el-input>
      </el-form-item>
          <el-form-item label="订单状态: 1未支付, 2已支付,  3已取消" prop="status">
          <el-input v-model="dataForm.status" placeholder="订单状态: 1未支付, 2已支付,  3已取消"></el-input>
      </el-form-item>
          <el-form-item label="支付金额" prop="payMoney">
          <el-input v-model="dataForm.payMoney" placeholder="支付金额"></el-input>
      </el-form-item>
          <el-form-item label="支付类型: 0微信付款1支付宝支付" prop="payType">
          <el-input v-model="dataForm.payType" placeholder="支付类型: 0微信付款1支付宝支付"></el-input>
      </el-form-item>
          <el-form-item label="支付时间" prop="payTime">
          <el-input v-model="dataForm.payTime" placeholder="支付时间"></el-input>
      </el-form-item>
          <el-form-item label="订单备注" prop="remark">
          <el-input v-model="dataForm.remark" placeholder="订单备注"></el-input>
      </el-form-item>
          <el-form-item label="创建时间" prop="createTime">
          <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
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
        detailId: '',
        termId: '',
        termName: '',
        studentName: '',
        schoolId: '',
        schoolName: '',
        gradeName: '',
        className: '',
        classId: '',
        phone: '',
        orderNo: '',
        status: '',
        payMoney: '',
        payType: '',
        payTime: '',
        remark: '',
        createTime: ''
      }
    }
  },
  computed: {
    dataRule () {
      return {
        detailId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        termId: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        termName: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        studentName: [
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
        phone: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        orderNo: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        status: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        payMoney: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        payType: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        payTime: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        remark: [
          { required: true, message: this.$t('validate.required'), trigger: 'blur' }
        ],
        createTime: [
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
      this.$http.get(`/water/order/${this.dataForm.id}`).then(({ data: res }) => {
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
        this.$http[!this.dataForm.id ? 'post' : 'put']('/water/order/', this.dataForm).then(({ data: res }) => {
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
