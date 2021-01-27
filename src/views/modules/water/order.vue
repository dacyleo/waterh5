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
        <el-form-item>
          <el-button type="info" @click="exportHandle()">{{ $t('export') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('water:order:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('water:order:delete')" type="danger" @click="deleteHandle()">{{ $t('deleteBatch') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="id" label="ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="detailId" label="学期详情表ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="termId" label="学期ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="termName" label="学期名称，如2020年上学期" header-align="center" align="center"></el-table-column>
        <el-table-column prop="studentName" label="学生姓名" header-align="center" align="center"></el-table-column>
        <el-table-column prop="schoolId" label="学校ID,即sys_user_id" header-align="center" align="center"></el-table-column>
        <el-table-column prop="schoolName" label="学校名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="gradeName" label="年级名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="className" label="班级名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="classId" label="班级ID" header-align="center" align="center"></el-table-column>
        <el-table-column prop="phone" label="手机号码" header-align="center" align="center"></el-table-column>
        <el-table-column prop="orderNo" label="订单编号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" label="订单状态: 1未支付, 2已支付,  3已取消" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payMoney" label="支付金额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payType" label="支付类型: 0微信付款1支付宝支付" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payTime" label="支付时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="remark" label="订单备注" header-align="center" align="center"></el-table-column>
        <el-table-column prop="createTime" label="创建时间" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('water:order:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="$hasPermission('water:order:delete')" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
          </template>
        </el-table-column>
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
