<template>
  <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false" :close-on-press-escape="false">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()" label-width="120px">
      <el-form-item prop="realName" label="学校名称">
        <el-input v-model="dataForm.realName" placeholder="学校名称"></el-input>
      </el-form-item>
      <el-form-item prop="username" label="登录账号" v-if="!dataForm.id">
        <el-input v-model="dataForm.username" placeholder="登录账号"></el-input>
      </el-form-item>
      <el-form-item prop="password" :label="$t('user.password')" :class="{ 'is-required': !dataForm.id }">
        <el-input v-model="dataForm.password" type="password" :placeholder="$t('user.password')"></el-input>
      </el-form-item>
      <el-form-item label="缴费金额" prop="payMoney">
        <el-input v-model="dataForm.payMoney" placeholder="缴费金额，单位元"></el-input>
      </el-form-item>
      <el-form-item label="收款账户" prop="incomeAccount">
        <el-select v-model="dataForm.incomeAccount" clearable >
          <el-option label="鸿浩润" value="1"></el-option>
          <el-option label="康健" value="2"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item prop="gradeAmount" label="年级数量" v-if="!dataForm.id">
        <el-input v-model="dataForm.gradeAmount" placeholder="年级数量"></el-input>
      </el-form-item>
      <el-form-item prop="classAmount" label="班级数量" v-if="!dataForm.id">
        <el-input v-model="dataForm.classAmount" placeholder="班级数量"></el-input>
      </el-form-item>
      <el-form-item prop="teacherPassword" label="班主任密码" :class="{ 'is-required': !dataForm.id }">
        <el-input v-model="dataForm.teacherPassword" type="password" placeholder="班主任密码"></el-input>
      </el-form-item>
      <el-form-item prop="status" :label="$t('user.status')" size="mini">
        <el-radio-group v-model="dataForm.status">
          <el-radio :label="0">{{ $t('user.status0') }}</el-radio>
          <el-radio :label="1">{{ $t('user.status1') }}</el-radio>
        </el-radio-group>
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
    data() {
      return {
        visible: false,
        dataForm: {
          id: '',
          username: '',
          password: '',
          comfirmPassword: '',
          teacherPassword: '',
          confirmTeacherPassword: '',
          realName: '',
          gender: 0,
          gradeAmount: 0,
          classAmount: 0,
          roleIdList: [],
          status: 1,
          payMoney: 0,
          incomeAccount: 1
        }
      }
    },
    computed: {
      dataRule() {
        return {

        }
      }
    },
    methods: {
      init() {
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
        this.$http.get(`/sys/user/${this.dataForm.id}`).then(({ data: res }) => {
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
          this.$http[!this.dataForm.id ? 'post' : 'put']('/sys/user', this.dataForm).then(({data: res}) => {
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
          }).catch(() => {
          })
        })
      }, 1000, {'leading': true, 'trailing': false})
    }
  }
</script>
