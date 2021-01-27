<template>
    <el-row>
        <el-col :span="24">
            <h1>欢迎使用水费缴纳后台管理系统</h1>
        </el-col>
    </el-row>
</template>

<script>
    export default {
        data() {
            return {
                orderMoney: '',
                shopProfit: '',
                orderAmount: '',
                shopData: {},
                visible: false,
                tempDate: '',
                dataForm: {
                    startTime: '',
                    endTime: ''
                }
            }
        },
        mounted() {
            // this.init()
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
