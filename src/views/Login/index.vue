<template>
  <div id="login">
    <div class="login-wrap">
      <ul class="menu-tab">
        <li
          :class="{ current: item.current }"
          @click="toggleMneu(item)"
          v-for="item in menuTab"
          :key="item.id"
        >
          {{ item.txt }}
        </li>
        <!-- 表单 开始 -->
        <el-form
          :model="ruleForm"
          status-icon
          :rules="rules"
          ref="ruleForm"
          class="login-form"
          size="medium"
        >
          <el-form-item prop="username" class="item-from">
            <label for="">邮箱</label>
            <el-input
              type="text"
              v-model="ruleForm.username"
              autocomplete="off"
            ></el-input>
          </el-form-item>
          <el-form-item prop="password" class="item-from">
            <label for="">密码</label>
            <el-input
              type="text"
              v-model="ruleForm.password"
              autocomplete="off"
              minlength="6"
              maxlength="20"
            ></el-input>
          </el-form-item>
          <el-form-item prop="passwords" class="item-from" v-show="model === 'register'">
            <label for="">重复密码</label>
            <el-input
              type="text"
              v-model="ruleForm.passwords"
              autocomplete="off"
              minlength="6"
              maxlength="20"
            ></el-input>
          </el-form-item>
          <el-form-item prop="code" class="item-from">
            <label for="">验证码</label>
            <el-row :gutter="10">
              <el-col :span="15">
                <el-input v-model.number="ruleForm.code"></el-input>
              </el-col>
              <el-col :span="9">
                <el-button type="success" class="block">获取验证码</el-button>
              </el-col>
            </el-row>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitForm('ruleForm')" class="login-btn block">登录</el-button>
          </el-form-item>
        </el-form>
      </ul>
    </div>
  </div>
</template>

<script>
import {stripscript,validateEmail, validatePass,validateVCode } from '/src/utils/validate.js'
export default {
  data() {
    // 验证用户名
    var validateUsername = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入用户名"));
      } else if (validateEmail(value)) {
        callback(new Error("用户名格式有误"));
      } else {
        callback();
      }
    };
    // 验证密码
    var validatePassword = (rule, value, callback) => {
      // 过滤后的数据
      this.ruleForm.password = stripscript(value)
      value = this.ruleForm.password;
      if (value === "") {
        callback(new Error("请输入密码"));
      } else if (validatePass(value)) {
        callback(new Error("输入密码为6-20位数字+字母格式!"));
      } else {
        callback();
      }
    };
    // 验证重复密码
    var validatePasswords = (rule, value, callback) => {
      // 如果模块值为login 直接通过
      if(this.model === 'login'){
        callback();
      }
      // 过滤后的数据
      this.ruleForm.passwords = stripscript(value)
      value = this.ruleForm.passwords;
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value != this.ruleForm.password) {
        callback(new Error("重复密码不正确!"));
      } else {
        callback();
      }
    };
    // 验证码
    var validateCode = (rule, value, callback) => {
      if (value === "") {
        return callback(new Error("请输入验证码"));
      } else if (validateVCode(value)) {
        return callback(new Error("验证码格式有误"));
      } else {
        callback();
      }
    };
    return {
      menuTab: [
        { txt: "登录", current: true,type:'login' },
        { txt: "注册", current: false,type:'register'},
      ],
      // 登录注册模块的值 
      model:'login',
      ruleForm: {
        username: "",
        password: "",
        passwords: "",
        code: "",
      },
      rules: {
        username: [{ validator: validateUsername, trigger: "blur" }],
        password: [{ validator: validatePassword, trigger: "blur" }],
        passwords: [{ validator: validatePasswords, trigger: "blur" }],
        code: [{ validator: validateCode, trigger: "blur" }],
      },
    };
  },

  components: {},
  // 写函数的区域
  methods: {
    toggleMneu(data) {
      this.menuTab.forEach((element, index) => {
        element.current = false;
      });
      //  高光
      data.current = true;
      // 修改登录注册模块的值切换
      this.model = data.type;
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          alert("submit!");
        } else {
          console.log("error submit!!");
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    },
  },
};
</script>

<style scoped lang="scss">
#login {
  height: 100vh;
  background: #344a5f;
}
.login-wrap {
  width: 330px;
  margin: auto;
}
.menu-tab {
  text-align: center;
  li {
    display: inline-block;
    width: 88px;
    line-height: 36px;
    font-size: 14px;
    color: #fff;
    border-radius: 2px;
    cursor: pointer;
  }
  .current {
    background-color: rgba(0, 0, 0, 0.1);
  }
}
.login-form {
  margin-top: 30px;
  label {
    display: block;
    color: white;
    font-size: 14px;
    // margin-bottom: 20px;
    text-align: left;
  }
  .item-from {
    margin-bottom: 14px;
  }
  .block {
    width: 100%;
    display: block;
  }
  .login-btn {
    margin-top: 20px;
  }
}
</style>
