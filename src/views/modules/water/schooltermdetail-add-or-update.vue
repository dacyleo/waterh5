<template>
  <el-dialog
    :visible.sync="visible"
    title="缴费二维码下载"
    :close-on-click-modal="false"
    :close-on-press-escape="false"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmitHandle()"
      :label-width="$i18n.locale === 'en-US' ? '120px' : '80px'"
    >
    </el-form>
    <div class="content" id="content">
      <div class="title">{{ dataForm.termName }}水费缴纳</div>
      <div class="qr-content">
        <div class="qr-img" id="qrcode"></div>
      </div>
      <div class="school">
        <div>{{ dataForm.schoolName }}</div>
        <div class="s-class">
          {{ dataForm.gradeName }}{{ dataForm.className }}
        </div>
      </div>
    </div>
    <template slot="footer">
     <div style="color:red;" v-if="loading">正在生成中..</div>
        <a v-if="!loading" class="down" href="" :download="dataForm.termName+'_'+dataForm.schoolName+'_'+dataForm.gradeName+'_'+dataForm.className+'.png'">下载二维码</a>
    </template>
  </el-dialog>
</template>

<script>
import QRCode from 'qrcodejs2';
import html2canvas from "html2canvas";
export default {
  data() {
    return {
      visible: false,
      loading:true,
      dataForm: {
        id: "",
        termId: "",
        termName: "",
        beginDate: "",
        endDate: "",
        schoolId: "",
        schoolName: "",
        gradeName: "",
        className: "",
        classId: "",
        needAmount: "",
        actualAmount: "",
        teacherId: "",
        teacherName: "",
        qrUrl: "",
      },
    };
  },
  watch:{
visible(val){
  if(val){
this.loading=true
  }
}
  },
  computed: {
    dataRule() {
      return {};
    },
  },
  methods: {
    init() {
      this.visible = true;
      this.$nextTick(() => {
        this.$refs["dataForm"].resetFields();
        if (this.dataForm.id) {
          this.getInfo();
        }
      });
    },
    // 获取信息
    getInfo() {
      this.$http
        .get(`/water/schooltermdetail/${this.dataForm.id}`)
        .then(({ data: res }) => {
          if (res.code !== 0) {
            return this.$message.error(res.msg);
          }
          this.dataForm = {
            ...this.dataForm,
            ...res.data,
          };
          this.initDownload();
        })
        .catch(() => {});
    },
    //
    initDownload() {
      let text = "https://water.cuihp.top/#/?id=" + this.dataForm.id;
       document.getElementById("qrcode").innerHTML = "";
      new QRCode('qrcode', {
        text: text,
        width: 200,
        height: 200,
        colorDark: "#000000",
        colorLight: "#ffffff",
        correctLevel: QRCode.CorrectLevel.H,
      });
       setTimeout(() => {
         html2canvas(document.getElementById('content')).then(canvas => {
            //canvas转换成url，然后利用a标签的download属性，直接下载，绕过上传服务器再下载
            document.querySelector(".down").setAttribute('href', canvas.toDataURL());
        });
        this.loading=false
       }, 500);
    },
  },
};
</script>
 <style>
.hide-view {
  /* visibility: hidden; */
}

.content {
  /* width: 620px; */
  background-color: green;
  padding-top: 50px;
}

.title {
  text-align: center;
  font-size: 32px;
  color: white;
}

.qr-content {
  display: flex;
  justify-content: center;
  margin-top: 30px;
  margin-bottom: 30px;
}

.qr-img {
  width: 220px;
  height: 220px;
  background-color: white;
  padding: 10px;
}

.school {
  background-color: white;
  padding: 40px;
  color: black;
  font-size: 26px;
  text-align: center;
}

.s-class {
  margin-top: 10px;
  font-size: 22px;
}
</style>
