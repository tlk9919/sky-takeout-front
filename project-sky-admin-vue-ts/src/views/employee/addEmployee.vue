<template>
  <div class="addBrand-container">
    <div class="container">
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="180px">
        <el-form-item label="账号" prop="username">
          <el-input v-model="ruleForm.username"></el-input>
        </el-form-item>
        <el-form-item label="员工姓名" prop="name">
          <el-input v-model="ruleForm.name"></el-input>
        </el-form-item>
        <el-form-item label="手机号" prop="phone">
          <el-input v-model="ruleForm.phone"></el-input>
        </el-form-item>
        <el-form-item label="性别" prop="sex">
            <el-radio v-model="ruleForm.sex" label="1">男</el-radio>
            <el-radio v-model="ruleForm.sex" label="2">女</el-radio>
        </el-form-item>
        <el-form-item label="身份证号" prop="idNumber">
          <el-input v-model="ruleForm.idNumber"></el-input>
        </el-form-item>
        <div class="subBox">
          <el-button type="primary" @click="submitForm('ruleForm',false)">保存</el-button>
          <el-button
            v-if="this.optType === 'add'"
            type="primary"
            @click="submitForm('ruleForm',true)">保存并继续添加员工
          </el-button>
          <el-button @click="() => this.$router.push('/employee')">返回</el-button>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script lang="ts">
import {addEmployee,getEmployeeById,editEmployee} from '@/api/employee'
export default {
  data(){
    return{
      optType: '',
      ruleForm: {
        username: '',
        name: '',
        phone: '',
        sex: '1',
        idNumber: ''
      },
      rules: {
        username: [
          { required: true, message: '请输入员工账号', trigger: 'blur' },
          { min: 3, max: 10, message: '长度在 3 到 10 个字符', trigger: 'blur' }
        ],
        name: [
          { required: true, message: '请输入员工姓名', trigger: 'blur' },
        ],
        phone: [
          { required: true, trigger: 'blur' , validator: (rule,vaule,callback)=>{
            if (vaule===''||(!/^1[3456789]\d{9}$/.test(vaule))){
              callback(new Error('手机号格式有误'))
            }
            else {
              callback()
            }
            }},
        ],
        sex: [
          { required: true, message: '请选择性别',}
        ],
        idNumber: [
          { required: true, trigger: 'blur' , validator: (rule,vaule,callback)=>{
              if (vaule===''||(!/^[1-9]\d{5}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/.test(vaule))){
                callback(new Error('身份证格式错误'))
              }
              else {
                callback()
              }
            }},
        ]
      }
    }
  },
  created(){
    // 判断是添加员工还是修改员工，获取路由参数
    this.optType = this.$route.query.id? 'update':'add'
    if (this.optType === 'update'){
      // 获取员工信息，用于页面回显
      getEmployeeById(this.$route.query.id).then(res=>{
        if (res.data.code === 1) {
          this.ruleForm = res.data.data
        }
      })
    }
  },
  methods:{
    submitForm(formName: string,isContinue:boolean){
      //表单校验
      this.$refs[formName].validate((valid: boolean)=>{
        if (valid) {
          //判断是新增还是修改
          if (this.optType === 'update'){
            editEmployee(this.ruleForm).then(res=>{
              if (res.data.code === 1) {
                this.$message.success('修改员工成功')
                this.$router.push('/employee')
              }
            })
          }
          else {
            addEmployee(this.ruleForm).then(res=>{
              if (res.data.code === 1) {
                this.$message.success('添加员工成功')
                if (isContinue){
                  this.ruleForm = {
                    username: '',
                    name: '',
                    phone: '',
                    sex: '1',
                    idNumber: ''
                  }
                }
                else {
                  this.$router.push('/employee')
                }
              }
              else {
                this.$message.error(res.data.msg)
              }
            })
          }
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.addBrand {
  &-container {
    margin: 30px;
    margin-top: 30px;
    .HeadLable {
      background-color: transparent;
      margin-bottom: 0px;
      padding-left: 0px;
    }
    .container {
      position: relative;
      z-index: 1;
      background: #fff;
      padding: 30px;
      border-radius: 4px;
      // min-height: 500px;
      .subBox {
        padding-top: 30px;
        text-align: center;
        border-top: solid 1px $gray-5;
      }
    }
    .idNumber {
      margin-bottom: 39px;
    }

    .el-form-item {
      margin-bottom: 29px;
    }
    .el-input {
      width: 293px;
    }
  }
}
</style>
