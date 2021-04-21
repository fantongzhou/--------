<template>
  <div class="box">
    <div id="login" style="width: 320px; height: 300px; text-align: center">
      <el-form :model="loginForm" ref="loginForm" :rules="rules">
        <el-form-item>
          <span style="color: white; font-family: 楷体; font-size: 26px"
            >用&nbsp;户&nbsp;登&nbsp;录&nbsp;</span
          >
        </el-form-item>
        <el-form-item prop="userName">
          <el-input
            type="text"
            v-model="loginForm.userName"
            placeholder="用户名"
          >
            <template slot="prepend"
              ><i class="el-icon-user" style="font-size: 20px; color: white"></i
            ></template>
          </el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input
            type="text"
            v-model="loginForm.password"
            placeholder="密码"
            show-password
          >
            <template slot="prepend"
              ><i class="el-icon-lock" style="font-size: 20px; color: white"></i
            ></template>
          </el-input>
        </el-form-item>
        <el-form-item>
          <el-button
            type="primary"
            size="medium"
            :loading="loading"
            style="font-size: 20px; font-family: 微软雅黑; width: 320px"
            @click="clickLogin"
            >登&nbsp;&nbsp;&nbsp;录</el-button
          >
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
 
 <script>
import { commonAPI } from "@/api/commonAPI";
export default {
  data() {
    var validateUserName = (rule, value, callback) => {
      if (value.length === 0) {
        return callback(new Error("请输入用户名"));
      } else {
        callback();
      }
    };
    var validatePassword = (rule, value, callback) => {
      if (value.length === 0) {
        callback(new Error("请输入密码"));
      } else if (value.length < 3) {
        callback(new Error("密码不能小于3位"));
      } else {
        callback();
      }
    };
    return {
      loginForm: {
        userName: "",
        password: "",
      },
      loading: false, //登陆加载效果
      rules: {
        userName: [
          {
            required: true,
            trigger: "blur",
            validator: validateUserName,
          },
        ],
        password: [
          {
            required: true,
            trigger: "blur",
            validator: validatePassword,
          },
        ],
      },
    };
  },
  methods: {
    clickLogin() {
      this.$refs.loginForm.validate((valid) => {
        if (valid) {
          //校验成功
          this.loading = true;
          
          setTimeout(() => {
            //为了看到登录转圈圈的加载效果，这里来一个延迟2秒发送请求
            //封装的公共请求API
            commonAPI("queryUser", this.loginForm)
              .then((res) => {
                let data = res.data;
                if (data.info.code === "0" && data.data === "OK") {
                  this.$router.push({
                    name: "layoutYHGL",
                  });
                } else {
                  this.$notify({
                    title: "登录提示",
                    message: "用户名或密码错误",
                    position: "bottom-right",
                    type: "error",
                  });
                  this.loading = false;
                }
              })
              .catch((error) => {
                this.loading = false;
                this.$notify({
                  //这里采用element ui的一个错误显示效果模板
                  title: "登录提示",
                  message: error.message,
                  position: "bottom-right",
                  type: "error",
                });
              });
          }, 2000);
        } else {
          return false;
        }
      });
    },
  },
};
</script>
 
 <style scoped="scoped">
.box {
  display: flex;
  height: 100%;
  justify-content: center;
  align-items: center;
}
</style>
 <style>
.el-input-group__prepend {
  padding: 0px 15px;
  background-color: #cccccc;
  border: 1 solid #72767b;
}

body {
  background-color: #72767b;
  margin: 0px;
}
</style>