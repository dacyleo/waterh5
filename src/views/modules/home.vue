<template>
    <el-card>
        <el-form :inline="true" :model="formInline" class="demo-form-inline">
            <el-form-item label="学期">
                <el-select v-model="dataForm.termId" placeholder="请选择学期" @change="getTermStat">
                    <el-option v-for="(item,index) in termList" :key="index" :label="item.name"
                               :value="item.id"></el-option>
                </el-select>
            </el-form-item>
<!--            <el-form-item>-->
<!--                <el-button type="primary" @click="getTermStat()">查询</el-button>-->
<!--            </el-form-item>-->
        </el-form>
        <div ref="echart1" style="height:400px;width:100%;"></div>
        <el-form :inline="true" :model="formInline" class="demo-form-inline">
            <el-form-item label="学校">
                <el-select v-model="dataForm.schoolId" placeholder="请选择学校" @change="getTermSchoolStat">
                    <el-option v-for="(item,index) in schoolList" :key="index" :label="item.realName"
                               :value="item.id"></el-option>
                </el-select>
            </el-form-item>
<!--            <el-form-item>-->
<!--                <el-button type="primary" @click="getTermSchoolStat()">查询</el-button>-->
<!--            </el-form-item>-->
        </el-form>


        <div ref="echart2" style="height:400px;width:100%;"></div>
    </el-card>


</template>
<script>
    import * as echarts from 'echarts';

    export default {
        data() {
            return {
                dataForm: {
                    termId: '1',
                    schoolId: '',
                },
                termList: [],
                schoolList: [],
                classList: [],
                termName: '',
                schoolName: '',
                schools: [],
                needs: [],
                classNeeds: [],
                actuals: [],
                classActuals: [],
                formInline: {
                    user: '',
                    region: ''
                }
            }
        },

        mounted() {
            this.getTermList()
            this.getSchoolList()
            this.init2()
        },
        methods: {
            getTermStat() {
                if (this.dataForm.termId == '') {
                    return this.$message.error('请选择学期')
                }
                this.$http.get(`/water/home/stat/` + this.dataForm.termId).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.termName = res.data.termName
                    this.schools = res.data.schools
                    this.actuals = res.data.actuals
                    this.needs = res.data.needs
                    this.init1()
                }).catch(() => {
                })
            },
            getTermSchoolStat() {
                if (this.dataForm.termId == '') {
                    return this.$message.error('请选择学期')
                }
                if (this.dataForm.schoolId == '') {
                    return this.$message.error('请选择学校')
                }
                this.$http.get(`/water/home/stat/` + this.dataForm.termId + `/` + this.dataForm.schoolId).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.schoolName = res.data.schoolName
                    this.classList = res.data.classes
                    this.classActuals = res.data.classActuals
                    this.classNeeds = res.data.classNeeds
                    this.init2()
                }).catch(() => {
                })
            },
            getTermList() {
                this.$http.get(`/water/home/termList`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.termList = {
                        ...this.termList,
                        ...res.data
                    }
                    this.getTermStat()
                }).catch(() => {
                })
            },
            getSchoolList() {
                this.$http.get(`/water/home/schoolList`).then(({data: res}) => {
                    if (res.code !== 0) {
                        return this.$message.error(res.msg)
                    }
                    this.schoolList = {
                        ...this.schoolList,
                        ...res.data
                    }
                }).catch(() => {
                })
            },
            init2() {
                let yd1 = []
                let yd2 = []
                for (let i = 0; i < 60; i++) {
                    yd1.push(Math.floor((Math.random() * 60) + 1))
                    yd2.push(Math.floor((Math.random() * 30) + 1))
                }

                var myChart = echarts.init(this.$refs.echart2);
                var option;
                option = {
                    title: {text: this.termName + this.schoolName + '缴费情况'},
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                            type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                        }
                    },
                    legend: {
                        data: ['已缴纳', '未缴纳']
                    },
                    grid: {
                        left: '3%',
                        right: '4%',
                        bottom: '3%',
                        containLabel: true
                    },
                    xAxis: [
                        {
                            type: 'category',
                            data: this.classList
                        }
                    ],
                    yAxis: [
                        {
                            type: 'value'
                        }
                    ],
                    series: [
                        {
                            name: '已缴纳',
                            type: 'bar',
                            stack: '广告',
                            emphasis: {
                                focus: 'series'
                            },
                            data: this.classActuals
                        },
                        {
                            name: '未缴纳',
                            type: 'bar',
                            stack: '广告',
                            emphasis: {
                                focus: 'series'
                            },
                            data: this.classNeeds
                        }

                    ]
                };
                option && myChart.setOption(option);
            },
            init1() {
                var myChart = echarts.init(this.$refs.echart1);
                var option;
                option = {
                    title: {text: this.termName + '各学校缴费情况'},
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                            type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                        }
                    },
                    legend: {
                        data: ['已缴纳', '未缴纳']
                    },
                    grid: {
                        left: '3%',
                        right: '4%',
                        bottom: '3%',
                        containLabel: true
                    },
                    xAxis: [
                        {
                            type: 'category',
                            data: this.schools
                        }
                    ],
                    yAxis: [
                        {
                            type: 'value'
                        }
                    ],
                    series: [
                        {
                            name: '已缴纳',
                            type: 'bar',
                            stack: '广告',
                            emphasis: {
                                focus: 'series'
                            },
                            data: this.actuals
                        },
                        {
                            name: '未缴纳',
                            type: 'bar',
                            stack: '广告',
                            emphasis: {
                                focus: 'series'
                            },
                            data: this.needs
                        }

                    ]
                };
                option && myChart.setOption(option);
            }
        },
        filters: {
            formatMoney: function (value) {
                if (!value) return '0.00'
                return value.toFixed(2)
            }
        }
    }
</script>

<style scoped lang="scss">

    .item-container {
        width: 100%;
        display: flex;
        flex-direction: row;
        /*justify-content: space-around;*/
        justify-content: space-between;
        margin-bottom: 40px;
    }

    .item {
        width: 200px;
        height: 120px;
        display: flow;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);
        font-size: 20px;
        line-height: 90px;
        text-align: center;
        border-radius: 4px;
        font-weight: bolder;
    }

    .item-title {
        border-radius: 4px 4px 0px 0px;
        width: 100%;
        height: 30px;
        line-height: 30px;
        font-size: 14px;
        font-weight: normal;
        background-color: #17B3A3;
        color: #f0f0f0;
    }

</style>
