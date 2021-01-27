<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-water__order}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.id" placeholder="id" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column prop="termName" label="学期名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="schoolName" label="学校名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="gradeName" label="年级名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="className" label="班级名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="studentName" label="学生姓名" header-align="center" align="center"></el-table-column>
        <el-table-column prop="phone" label="手机号码" header-align="center" align="center"></el-table-column>
        <el-table-column prop="orderNo" label="订单编号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="订单状态" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payMoney" label="支付金额" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="payType" label="支付类型: 0微信付款1支付宝支付" header-align="center" align="center"></el-table-column>-->
        <el-table-column prop="payTime" label="支付时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="下单时间" header-align="center" align="center"></el-table-column>
      </el-table>
      <el-pagination
        :current-page="page"
        :page-sizes="[10, 20, 50, 100]"
        :page-size="limit"
        :total="total"
        layout="total, sizes, prev, pager, next, jumper"
        @size-change="pageSizeChangeHandle"
        @current-change="pageCurrentChangeHandle">
      </el-pagination>
      <!-- 弹窗, 新增 / 修改 -->
      <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
    </div>
  </el-card>
</template>

<script>
import mixinViewModule from '@/mixins/view-module'
import AddOrUpdate from './order-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/water/order/page',
        getDataListIsPage: true,
        exportURL: '/water/order/export',
        deleteURL: '/water/order',
        deleteIsBatch: true
      },
      dataForm: {
        id: ''
      }
    }
  },
  components: {
    AddOrUpdate
  }
}
</script>
