<template>
  <div id="app">
    <el-button type="primary" plain @click="visible = true; active = 1">注册</el-button>
    <el-dialog title="注册" :visible.sync="visible" :close-on-click-modal="false">
      <el-steps :active="active">
        <el-step title="第 1 步" description="填写信息"></el-step>
        <el-step title="第 2 步" description="上传证明文件"></el-step>
        <el-step title="注册成功"></el-step>
      </el-steps>
      <el-form ref="dataForm" :model="dafaForm" :rules="formRules" label-position="left" label-width="100px" style="width: 400px; 
    margin-left: 50px">

        <div v-show="active == 1">
          <el-form-item label="用户名" prop="uName">
            <el-input v-model="dafaForm.uName" />
          </el-form-item>
          <el-form-item label="密码" prop="psw">
            <el-input type="password" v-model="dafaForm.psw" />
          </el-form-item>
          <el-form-item label="确认密码" prop="confPsw">
            <el-input type="password" v-model="dafaForm.confPsw" />
          </el-form-item>
          <el-form-item label="电话号码" prop="tel">
            <el-input v-model="dafaForm.tel" />
          </el-form-item>
          <el-form-item label="证件类型" prop="idType">
            <el-select v-model="dafaForm.idType" placeholder="请选择证件类型">
              <el-option label="身份证" value="IDCard"></el-option>
              <el-option label="护照" value="Passport"></el-option>
              <el-option label="驾照" value="DriverLicense"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="证件号码" prop="idNum">
            <el-input v-model="dafaForm.idNum" />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="nextStep">下一步</el-button>
          </el-form-item>
        </div>

        <div v-show="active == 2">
          <el-upload class="upload-demo" drag action="#" :before-upload="beforeUpload" :on-success="handleSuccess"
            :on-preview="handlePreview" :on-remove="handleRemove" :file-list="fileList" :auto-upload="true"
            :http-request="mockHttpRequest" :on-change="handleChange">
            <i class="el-icon-upload"></i>
            <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
            <div class="el-upload__tip" slot="tip">只能上传pdf/doc/docx文件，且不超过10MB</div>
          </el-upload>
          <el-button type="plain" @click="preStep">上一步</el-button>
          <el-button type="primary" @click="handleSubmit">完成注册</el-button>
        </div>

      </el-form>

    </el-dialog>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {

  },
  data() {
    return {
      visible: false,
      active: 1,
      dafaForm: {
        uName: "",
        psw: "",
        confPsw: "",
        tel: "",
        idType: "",
        idNum: ""
      },
      fileList: [],
      formRules: {
        uName: [{ required: true, message: '请输入用户名', trigger: 'blur' }],
        psw: [{ required: true, message: '请输入密码', trigger: 'blur' }],
        confPsw: [
          { required: true, message: '请确认密码', trigger: 'blur' },
          { validator: this.validateConfirmPassword, trigger: 'blur' }
        ],
        tel: [{ required: true, message: '请输入电话号码', trigger: 'blur' }],
        idType: [{ required: true, message: '请选择证件类型', trigger: 'change' }],
        idNum: [{ required: true, message: '请输入证件号码', trigger: 'blur' }],
      }
    };
  },
  methods: {
    validateConfirmPassword(rule, value, callback) {
      if (value !== this.dafaForm.psw) {
        callback(new Error('两次输入的密码不一致'));
      } else {
        callback();
      }
    },
    nextStep() {
      this.$refs.dataForm.validate((valid) => {
        if (valid) {
          this.active = 2
        }
      });
    },
    preStep() {
      this.active = 1;
    },
    handleChange(file, fileList) {
      this.fileList = fileList;
    },
    beforeUpload(file) {
      const isDocOrPdf = file.type === 'application/pdf' ||
        file.type === 'application/msword' ||
        file.type === 'application/vnd.openxmlformats-officedocument.wordprocessingml.document';
      const isLt10M = file.size / 1024 / 1024 < 10;

      if (!isDocOrPdf) {
        this.$message.error('上传文件只能是 doc、docx、pdf 格式!');
        return false;
      }
      if (!isLt10M) {
        this.$message.error('上传文件大小不能超过 10MB!');
        return false;
      }
      console.log("可以上传")
      return true;
    },
    handleSuccess(response, file, fileList) {
      this.fileList = fileList;
      this.$message.success('文件上传成功');
    },
    handlePreview(file) {
      console.log(file);
    },
    handleRemove(file, fileList) {
      console.log(file, fileList);
      this.fileList = fileList;
    },
    beforeRemove(file, fileList) {
      return this.$confirm(`确定移除 ${file.name}？`);
    },
    handleSubmit() {
      if (this.fileList.length === 0) {
        this.$message.error('请上传资质证明材料');
        return;
      }
      // 提交注册逻辑
      this.$message.success('注册成功！');
      this.active = 3;
      this.dafaForm = {
        uName: "",
        psw: "",
        confPsw: "",
        tel: "",
        idType: "",
        idNum: ""
      },
        this.fileList = []
    },
    mockHttpRequest({ file, onSuccess }) {
      setTimeout(() => {
        onSuccess('上传成功', file);
      }, 1000);
    }
  }
}
</script>

<style>
.upload-demo .el-upload {
  border: 1px dashed #d9d9d9;
  border-radius: 6px;
  background-color: #f5f5f5;
  text-align: center;
}

.upload-demo .el-upload__text {
  color: #666;
  font-size: 14px;
}

.upload-demo em {
  color: #409EFF;
  font-style: normal;
}
</style>
