<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-water__schoolclass}">
      <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
        <el-form-item>
          <el-input v-model="dataForm.sysUserName" placeholder="学校名称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-select v-model="dataForm.schGrade" placeholder="年级" clearable >
            <el-option label="全部" value=""></el-option>
            <el-option label="1年级" value="1"></el-option>
            <el-option label="2年级" value="2"></el-option>
            <el-option label="3年级" value="3"></el-option>
            <el-option label="4年级" value="4"></el-option>
            <el-option label="5年级" value="5"></el-option>
            <el-option label="6年级" value="6"></el-option>
            <el-option label="7年级" value="7"></el-option>
            <el-option label="8年级" value="8"></el-option>
            <el-option label="9年级" value="9"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList()">{{ $t('query') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table v-loading="dataListLoading" :data="dataList" border @selection-change="dataListSelectionChangeHandle" style="width: 100%;">
        <el-table-column prop="sysUserName" label="学校名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="schGrade" label="年级" header-align="center" align="center"></el-table-column>
        <el-table-column prop="schClass" label="班级" header-align="center" align="center"></el-table-column>
        <el-table-column prop="teacherUserName" label="班主任账号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="needAmount" label="应缴人数" header-align="center" align="center"></el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button type="text" size="small" @click="editNeedAmount(scope.row)">应缴人数</el-button>
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
import AddOrUpdate from './schoolclass-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/water/schoolclass/page',
        getDataListIsPage: true,
        exportURL: '/water/schoolclass/export',
        deleteURL: '/water/schoolclass',
        editNeedAmountURL: '/water/schoolclass/editNeedAmountURL',
        deleteIsBatch: true
      },
      dataForm: {
        sysUserName: '',
        schGrade: ''
      }
    }
  },
  components: {
    AddOrUpdate
  },
  methods: {
    editNeedAmount: function (row) {
      this.$prompt('请输入'+ row.schGrade + '年级' + row.schClass + '班' +'应缴人数', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消'
      }).then(({value}) => {
        this.$http.post(`${this.mixinViewModuleOptions.editNeedAmountURL}` + '?id=' + row.id + '&needAmount=' +value
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
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '取消输入'
        });
      });
    }
  }
}
</script>
