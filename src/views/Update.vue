<template>
  <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
    <el-form-item label="编号" >
      <el-input v-model="ruleForm.serialid" readonly></el-input>
    </el-form-item>
    <el-form-item label="文件名" prop="sqlFileName">
      <el-input v-model="ruleForm.sqlFileName"></el-input>
    </el-form-item>
    <el-form-item label="文件路径" prop="sqlFilePath">
      <el-input v-model="ruleForm.sqlFilePath"></el-input>
    </el-form-item>
    <el-form-item label="文件编码" prop="sqlFileCharset">
      <el-input v-model="ruleForm.sqlFileCharset"></el-input>
    </el-form-item>
    <el-form-item label="文件执行状态" prop="sqlFileExecuteFlag">
      <el-input v-model="ruleForm.sqlFileExecuteFlag"></el-input>
    </el-form-item>
    <el-form-item label="文件执行日志" prop="sqlFileExecuteLog">
      <el-input v-model="ruleForm.sqlFileExecuteLog"></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitForm('ruleForm')">修改</el-button>
      <el-button @click="resetForm('ruleForm')">重置</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  data() {
    return {
      ruleForm: {
        serialid: '',
        sqlFileName: '',
        sqlFilePath: '',
        sqlFileCharset: '',
        sqlFileExecuteFlag: '',
        sqlFileExecuteLog: ''
      },
      rules: {
        sqlFileExecuteFlag: [
          { required: true, message: '请输入文件执行状态', trigger: 'blur' },
        ]
      }
    };
  },
  methods: {
    submitForm(formName) {
      const _this = this;
      this.$refs[formName].validate((valid) => {
        if (valid) {
          axios.put('http://localhost:8081/stock/update',this.ruleForm).then(response=>{
            if(response.data.resultcode ===0){
              _this.$message("修改成功!!");
              _this.$router.push('/pageOne');
            }
          })
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  },
  created(){
    const _this = this;
    axios.get("http://localhost:8081/stock/findById/" + this.$route.query.id).then(response=>{
      console.log("=====>",response.data)
      _this.ruleForm = response.data;
    });
  }
}
</script>
