<template>
    <el-dialog :visible.sync="visible" :title="!dataForm.id ? $t('add') : $t('update')" :close-on-click-modal="false"
               :close-on-press-escape="false">
        <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmitHandle()"
                 :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'">
            <el-form-item label="学期名称" prop="name">
                <el-input v-model="dataForm.name" placeholder="学期名称，如2020年上学期"></el-input>
            </el-form-item>
            <el-form-item label="缴费金额" prop="payMoney">
                <el-input v-model="dataForm.payMoney" placeholder="缴费金额，单位元"></el-input>
            </el-form-item>
            <el-form-item label="开始时间" prop="beginDate">
                <el-date-picker
                        v-model="dataForm.beginDate"
                        type="datetime"
                        placeholder="缴费开始时间">
                </el-date-picker>
            </el-form-item>
            <el-form-item label="结束时间" prop="endDate">
                <el-date-picker
                        v-model="dataForm.endDate"
                        type="datetime"
                        placeholder="缴费结束时间">
                </el-date-picker>
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
                    name: '',
                    payMoney: '',
                    beginDate: '',
                    endDate: '',
                    createTime: ''
                }
            }
        },
        computed: {
            dataRule() {
                return {
                    name: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    payMoney: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    beginDate: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ],
                    endDate: [
                        {required: true, message: this.$t('validate.required'), trigger: 'blur'}
                    ]
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
            getInfo() {
                this.$http.get(`/water/schoolterm/${this.dataForm.id}`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.dataForm = {
                        ...this.dataForm,
                        ...res.data
                    }
                }).catch(() => {
                })
            },
            // 表单提交
            dataFormSubmitHandle: debounce(function () {
                this.$refs['dataForm'].validate((valid) => {
                    if (!valid) {
                        return false
                    }
                    this.dataForm.beginDate = this.$moment(this.dataForm.beginDate).format('YYYY-MM-DD h:mm:ss');
                    this.dataForm.endDate = this.$moment(this.dataForm.endDate).format('YYYY-MM-DD h:mm:ss');
                    this.$http[!this.dataForm.id ? 'post' : 'put']('/water/schoolterm/', this.dataForm).then(({data: res}) => {
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
