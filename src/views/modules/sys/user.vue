<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-sys__user">
      <el-form :inline="true" :model="dataForm">
        <el-form-item>
          <el-input v-model="dataForm.realName" placeholder="学校名称" clearable></el-input>
        </el-form-item>
        <el-form-item>
          <el-button @click="getDataList2()">{{ $t('query') }}</el-button>
        </el-form-item>
        <el-form-item>
          <el-button v-if="$hasPermission('sys:user:save')" type="primary" @click="addOrUpdateHandle()">{{ $t('add') }}</el-button>
        </el-form-item>
      </el-form>
      <el-table
        v-loading="dataListLoading"
        :data="dataList"
        border
        @selection-change="dataListSelectionChangeHandle"
        @sort-change="dataListSortChangeHandle"
        style="width: 100%;">
        <el-table-column type="selection" header-align="center" align="center" width="50"></el-table-column>
        <el-table-column prop="username" label="登录账号" header-align="center" align="center"></el-table-column>
        <el-table-column prop="realName" label="学校名称" header-align="center" align="center"></el-table-column>
        <el-table-column prop="gradeAmount" label="年级数量" header-align="center" align="center"></el-table-column>
        <el-table-column prop="classAmount" label="班级数量" header-align="center" align="center"></el-table-column>
        <el-table-column prop="status" :label="$t('user.status')"  header-align="center" align="center">
          <template slot-scope="scope">
            <el-tag v-if="scope.row.status === 0" size="small" type="danger">{{ $t('user.status0') }}</el-tag>
            <el-tag v-else size="small" type="success">{{ $t('user.status1') }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button v-if="$hasPermission('sys:user:update')" type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">{{ $t('update') }}</el-button>
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
import AddOrUpdate from './user-add-or-update'
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/sys/user/page',
        getDataListIsPage: true,
        deleteURL: '/sys/user',
        deleteIsBatch: true,
        exportURL: '/sys/user/export'
      },
      dataForm: {
        username: ''
      }
    }
  },
  components: {
    AddOrUpdate
  },
  methods: {
    getDataList2(){
      this.page = 1
      this.getDataList()
    }
  }
}
</script>
