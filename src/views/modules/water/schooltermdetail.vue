<template>
  <el-card shadow="never" class="aui-card--fill">
    <div class="mod-water__schooltermdetail}">
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
        <el-table-column prop="needAmount" label="应缴人数" header-align="center" align="center"></el-table-column>
        <el-table-column prop="actualAmount" label="实缴人数" header-align="center" align="center"></el-table-column>
<!--        <el-table-column prop="teacherName" label="班主任姓名" header-align="center" align="center"></el-table-column>-->
        <el-table-column :label="$t('handle')" fixed="right" header-align="center" align="center" width="150">
          <template slot-scope="scope">
            <el-button type="text" size="small" @click="downloadQRCode(scope.row.id)">二维码下载</el-button>
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
import AddOrUpdate from './schooltermdetail-add-or-update'
import qs from "qs";
import Cookies from "js-cookie";
export default {
  mixins: [mixinViewModule],
  data () {
    return {
      mixinViewModuleOptions: {
        getDataListURL: '/water/schooltermdetail/page',
        getDataListIsPage: true,
        exportURL: '/water/schooltermdetail/export',
        deleteURL: '/water/schooltermdetail',
        downloadURL: '/water/schooltermdetail/download',
        deleteIsBatch: true
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
    downloadQRCode: function (id) {
      this.dataForm.id = id
      var params = qs.stringify({
        'token': Cookies.get('token'),
        ...this.dataForm
      })
      window.location.href = `${window.SITE_CONFIG['apiURL']}` + this.mixinViewModuleOptions.downloadURL + '?' + `${params}`
    }
  }
}
</script>
