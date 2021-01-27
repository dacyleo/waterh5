<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-water__schoolterm}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.name" placeholder="学期名称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('water:schoolterm:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="name" label="学期名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="payMoney" label="缴费金额" header-align="center" align="center"></el-table-column>
        <el-table-column prop="beginDate" label="缴费开始时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="endDate" label="缴费结束时间" header-align="center" align="center"></el-table-column>
        <el-table-column prop="state" label="状态" :formatter="formatState" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="scope.row.state == 0" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
            <el-button v-if="scope.row.state == 0" type="text" size="small" @click="deleteHandle(scope.row.id)">{{ $t('delete') }}</el-button>
            <el-button v-if="scope.row.state == 0" type="text" size="small" @click="startTerm(scope.row.id)">开启</el-button>
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
import AddOrUpdate from './schoolterm-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/water/schoolterm/page',
        getDataListIsPage: true,
        exportURL: '/water/schoolterm/export',
        deleteURL: '/water/schoolterm',
        deleteIsBatch: true,
        startURL: '/water/schoolterm/start'
      },
      dataForm: {
        id: ''
      }
    }
  },
  components: {
    AddOrUpdate
  },
  methods: {
    startTerm: function(id){
      this.$confirm('确定开启本学期缴费?', { 'handle': '上架' }, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http.post(`${this.mixinViewModuleOptions.startURL}` + '?id=' + id
        ).then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg)
          }
          this.$message({
            message: this.$t('prompt.success'),
            type: 'success',
            duration: 1000,
            onClose: () => {
              this.getDataList()
            }
          })
        })
      })
    },
    formatState: function (row) {
      if(row.state == null){
        return '未知'
      }
      if(row.state == 0){
        return '未开始'
      }else if(row.state == 1){
        return '已开始'
      }else{
        return '未知'
      }
    }
  }
}
</script>
