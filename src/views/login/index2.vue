<template>
    <div id="login">
       <div class="login-wrap">
           <ul class="menu-tab">
               <li :class="{'current':item.current}" v-for="(item,index) in menuTab" :key=index @click="toggleMenu(item)">{{item.tex}}</li>
           </ul>
             <!-- 表单 start -->
            <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" class="login-form" size="medium">
                    <el-form-item prop="username" class="item-from">
                        <label>邮箱</label>
                        <el-input type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
                    </el-form-item>
                    <el-form-item prop="password" class="item-from">
                        <label>密码</label>
                        <el-input type="text" v-model="ruleForm.password" autocomplete="off" minlength="6" maxlength="20"></el-input>
                    </el-form-item>
                    <el-form-item prop="password" class="item-from" v-if=" model === 'register'">
                        <label>重复密码</label>
                        <el-input type="text" v-model="ruleForm.passwords" autocomplete="off" minlength="6" maxlength="20"></el-input>
                    </el-form-item>
                    <el-form-item prop="code" class="item-from">
                      <label>验证码</label>
                      <el-row :gutter="10">
                        <el-col :span="15"><el-input v-model.number="ruleForm.code" minlength="6" maxlength="6"></el-input></el-col>
                        <el-col :span="9"><el-button type="success">获取验证码</el-button></el-col>
                      </el-row>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="submitForm('ruleForm')" class="block">提交</el-button>
                    </el-form-item>
                </el-form>
        <!-- 表单 end -->
       </div>
    </div>
</template>

<script>
import { stripscript,validateEmail,validatePass,validateCode } from "@/utils/validate.js"
import { reactive,ref } from '@vue/composition-api'
// vue中是数据驱动视图   js dom元素操作
export default {
    name: "login",
    //setup(prpos,context){}  这里面放置data数据，生命周期函数，自定义函数。
    //声明一个对象的方法reactive
    // const menuTab = reactive{[
    //             { tex:"登录",current:true,type:'login'},
    //             { tex:"注册",current:false,type:'register'},
    //         ]};
    setup:()=>{
       const menuTab = reactive([
                { tex:"登录",current:true,type:'login'},
                { tex:"注册",current:false,type:'register'},
            ])
      console.log(menuTab);
      const model = ref('login');
      console.log(model.value);
    },
    data: ()=>{
      //验证用户名
      var validateUsername = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入用户名'));
        } else if(validateEmail(value)){
          callback(new Error('用户名格式有误！'));
        }else{
          if (this.ruleForm.checkPass !== '') {
            this.$refs.ruleForm.validateField('checkPass');
          }
          callback();
        }
      };
      //密码验证
      var validatePassword = (rule, value, callback) => {
        console.log(this);
        //过滤掉多余字符
        // this.ruleForm.password = stripscript(value); 
        // console.log(this.ruleForm.password);
        value = stripscript(value);
        console.log(stripscript(value));
        if (value === '') {
          callback(new Error('请输入密码'));
        } else if (validatePass(value)) {
          callback(new Error('密码格式为6-20为数字+字母'));
        } else {
          callback();
        }
      };
      //验证重复密码
       var validatePasswords = (rule, value, callback) => {
        console.log(this);
        //如果是login的话，直接让通过，不要验证
        // if(this.model === 'login'){
        //   callback();
        // }
        //过滤掉多余字符
        // this.ruleForm.passwords = stripscript(value); 
        // console.log(this.ruleForm.passwords);
        value = stripscript(value);
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value != this.ruleForm.password) {
          callback(new Error('重复密码不正确'));
        } else {
          callback();
        }
      };
      //验证码验证
       var checkCode = (rule, value, callback) => {
        if (value === '') {
          return callback(new Error('验证码不能为空'));
        }else if(validateCode(value)){
          callback(new Error('验证码格式有误'));
        }else{
          callback();
        }
      };
        return {
            menuTab:[
                { tex:"登录",current:true,type:'login'},
                { tex:"注册",current:false,type:'register'},
            ],
            //显示模块的值
            model:"login",
            ruleForm: {
                username: '',
                password: '',
                code: '',
                passwords: '',
            },
            rules: {
                username: [
                    { validator: validateUsername, trigger: 'blur' }
                ],
                password: [
                    { validator: validatePassword, trigger: 'blur' }
                ],
                passwords: [
                    { validator: validatePasswords, trigger: 'blur' }
                ],
                code: [
                    { validator: checkCode, trigger: 'blur' }
                ]
            }
      } 
    },
    //挂载完成后自动执行的
    mounted:()=>{

    },
    methods:{
        toggleMenu:function(data){
            this.menuTab.forEach(element => {
                element.current = false;
            });
            //高光
            data.current = true;
            //修改模块值
            this.model = data.type;
        },
        submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
    },
};
</script>

<style lang="scss" scoped>
    #login{
        height:100vh;
        background-color: #44C6FF;
    }
    .login-wrap{
        width:330px;
        margin:auto;
    }
    .menu-tab{
        text-align: center;
        li{
            display: inline-block;
            width: 88px;
            line-height: 36px;
            font-size: 14px;
            color: #fff;
            border-radius: 2px;
            cursor: pointer;
        }
    }
    .current{
        background-color: #19A15F;
    }
    .login-form{
        margin-top: 20px;
        .item-from{
          margin-bottom:13px;
        }
          label{
              display:block;
              margin-bottom: 3px;
              color:#fff;
              font-size: 14px;
          }
          .block{
              display: block;
              margin-top: 20px;
              width: 100%;
          }
    }
</style>