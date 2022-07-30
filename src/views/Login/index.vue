<template>
  <div id="login">
    <div class="login-wrap">
      <ul class="menu-tab">
        <li
          :class="{ current: item.current }"
          :key="item.id"
          v-for="(item, index) in menuTab"
          @click="toggleMenu(index)"
        >
          {{ item.txt }}
        </li>
      </ul>
      <!-- 登录 -->
      <el-form
        :model="ruleForm"
        :rules="rules"
        class="login-form"
        label-position="top"
        ref="ruleForm"
        size="medium"
        status-icon
      >
        <!-- 邮箱 -->
        <el-form-item class="item-from" prop="username">
          <label>邮箱</label>
          <el-input
            autocomplete="off"
            maxlength="20"
            minlength="6"
            type="email"
            v-model="ruleForm.username"
          ></el-input>
        </el-form-item>

        <!-- 密码 -->
        <el-form-item class="item-from" prop="password">
          <label>密码</label>
          <el-input
            autocomplete="off"
            maxlength="20"
            minlength="6"
            type="password"
            v-model="ruleForm.password"
          ></el-input>
        </el-form-item>

        <!-- 重复密码 -->
        <el-form-item
          class="item-from"
          prop="passwords"
          v-show="mode == 'register'"
        >
          <label>重复密码</label>
          <el-input
            autocomplete="off"
            maxlength="20"
            minlength="6"
            type="password"
            v-model="ruleForm.passwords"
          ></el-input>
        </el-form-item>

        <!-- 验证码 -->
        <el-form-item class="item-from" prop="code">
          <label>验证码</label>

          <el-row :gutter="11">
            <el-col :span="15">
              <el-input
                maxlength="6"
                minlength="6"
                v-model.number="ruleForm.code"
              ></el-input>
            </el-col>
            <el-col :span="9">
              <el-button class="block" type="success">获取验证码</el-button>
            </el-col>
          </el-row>
        </el-form-item>
        <el-form-item>
          <el-button
            @click="submitForm('ruleForm')"
            class="login-btn block"
            type="danger"
            >提交</el-button
          >
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
import {
  stripscript,
  validatePass,
  validateEmail,
  validateVCode
} from "@/utils/validate";

export default {
  name: "login",
  //组件，有引入组件时，放置组件名称。
  components: {},
  data() {
    // 验证用户名
    let validateUsername = (rule, value, callback) => {
      if (value === "") {
        callback(new Error("请输入用户名"));
      } else if (validateEmail(value)) {
        callback(new Error("用户名格式有误"));
      } else {
        callback(); //true
      }
    };
    // 验证密码
    let validatePassword = (rule, value, callback) => {
      // 过滤后的数据
      this.ruleForm.password = stripscript(value);
      value = this.ruleForm.password;
      if (value === "") {
        callback(new Error("请输入密码"));
      } else if (validatePass(value)) {
        callback(new Error("密码为6至20位数字+字母"));
      } else {
        callback();
      }
    };
    // 验证重复密码
    let validatePasswords = (rule, value, callback) => {
      // 如果模块值为login, 直接通过
      if (this.mode === "login") {
        callback();
      }
      // 过滤后的数据
      this.ruleForm.passwords = stripscript(value);
      value = this.ruleForm.passwords;
      if (value === "") {
        callback(new Error("请再次输入密码"));
      } else if (value != this.ruleForm.password) {
        callback(new Error("重复密码不正确"));
      } else {
        callback();
      }
    };
    // 验证验证码
    let validateCode = (rule, value, callback) => {
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
        { txt: "登录", current: true, mode: "login" },
        { txt: "注册", current: false, mode: "register" }
      ],
      mode: "login",
      isActive: true,
      ruleForm: {
        username: "410293095@qq.com",
        password: "wo123456789",
        passwords: "",
        code: ""
      },
      rules: {
        username: [{ validator: validateUsername, trigger: "blur" }],
        password: [{ validator: validatePassword, trigger: "blur" }],
        passwords: [{ validator: validatePasswords, trigger: "blur" }],
        code: [{ validator: validateCode, trigger: "blur" }]
      }
    };
  },
  //创建完成时（生命周期其中一个）
  created() {},
  //挂载完成时（生命周其其中一个）
  mounted() {},
  //写函数的
  methods: {
    //切换登录注册
    toggleMenu(index) {
      this.menuTab.forEach((ele, i) => {
        ele.current = i == index ? true : false;
      });
      this.mode = this.menuTab[index].mode;
    },
    //提交登录
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
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
    }
  }
};
</script>
<style lang="scss" scoped>
#login {
  height: 100vh;
  background-color: #344a5f;
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
  margin-top: 29px;
  label {
    display: block;
    margin-bottom: 3px;
    font-size: 14px;
    color: #fff;
  }
  .item-from {
    margin-bottom: 13px;
  }
  .block {
    display: block;
    width: 100%;
  }
  .login-btn {
    margin-top: 19px;
  }
}
</style>
<!--
密码加密：
1、在前端预先加密一次
登录的密码：123456（普通字符串）
经过加密后：sha1('123456') == '541216ad5s4f5ds1f5asd4f65asd4' （加密后的字符串）


2、后台加密
接收到字符串：'541216ad5s4f5ds1f5asd4f65asd4'
后台再次加密：md5('541216ad5s4f5ds1f5asd4f65asd4') == '8f9qwersd3g165y4d1sf3s1f6aew4'（最终的加密后的密码）
最终新的字符串写入数据库：8f9qwersd3g165y4d1sf3s1f6aew4


3、登录
用户名与加密后的密码进行匹配，成功则登录，失败则提示
-->
