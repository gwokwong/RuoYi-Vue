<template>

  <div>

  <!-- <div class="accountbg"></div>  -->

    <!-- Begin page -->

    <div class="wrapper-page">

        <div class="container">
            <div class="row align-items-center justify-content-center">
                <div class="col-lg-5">
                    <div class="card card-pages shadow-none mt-4">
                        <div class="card-body">
                            <div class="text-center mt-0 mb-3">
                                <a href="index.html" class="logo logo-admin">
                                    <img src="@/assets/static/picture/logo-dark.png" class="mt-3" alt="" height="26"></a>
                                <p class="text-muted w-75 mx-auto mb-4 mt-4">输入您的账号址和密码以访问管理面板</p>
                            </div>

                            <el-form ref="loginForm" :model="loginForm" :rules="loginRules" class="form-horizontal mt-4">
                                 <el-form-item prop="username" class="form-group">
                                    <div class="col-12">
                                       <label for="username">账号</label>
                                       <input class="form-control" v-model="loginForm.username" type="text" required="" id="username" placeholder="请输入账号"  ref="username">
                                    </div>
                                </el-form-item>


                                <el-form-item prop="password" class="form-group">
                                    <div class="col-12">
                                       <label for="password">密码</label>
                                       <input class="form-control" v-model="loginForm.password" type="password" required="" id="password" placeholder="请输入密码"  ref="password">
                                    </div>
                                </el-form-item>

                                <div class="form-group">
                                    <div class="col-12">
                                        <div class="checkbox checkbox-primary">
                                            <div class="custom-control custom-checkbox">
                                                <input type="checkbox" class="custom-control-input" id="customCheck1">
                                                <label class="custom-control-label" for="customCheck1"> 记住密码</label>
                                            </div>
                                        </div>
                                    </div>
                                </div>

                                  <el-form-item class="form-group text-center mt-3">
                                    <div class="col-12">
                                        <button class="btn btn-primary btn-block waves-effect waves-light"  type="primary" @click="handleLogin">登录</button>
                                    </div>
                                </el-form-item>

                            </el-form>

                        </div>

                    </div>

                </div>
            </div>
            <!-- end row -->
        </div>
    </div>

    </div>

</template>

<script src='@/assets/static/js/jquery.min.js'></script>
<script src="@/assets/static/js/bootstrap.bundle.min.js"></script>
<script src="@/assets/static/js/jquery.slimscroll.js"></script>
<script src="@/assets/static/js/waves.min.js"></script>
<script src="@/assets/static/js/apexcharts.min.js"></script>
<script src="@/assets/static/js/bootstrap-datepicker.min.js"></script>
<script src="@/assets/static/js/app.js"></script>

<script>
import { getCodeImg } from "@/api/login";
import Cookies from "js-cookie";
import { encrypt, decrypt } from '@/utils/jsencrypt'

export default {
  name: "Login",
  data() {
    return {
      codeUrl: "",
      loginForm: {
        username: "admin",
        password: "admin123",
        rememberMe: false,
        code: "",
        uuid: ""
      },
      loginRules: {
        username: [
          { required: true, trigger: "blur", message: "请输入您的账号" }
        ],
        password: [
          { required: true, trigger: "blur", message: "请输入您的密码" }
        ],
        code: [{ required: true, trigger: "change", message: "请输入验证码" }]
      },
      loading: false,
      // 验证码开关
      captchaOnOff: false,
      // 注册开关
      register: false,
      redirect: undefined
    };
  },
  watch: {
    $route: {
      handler: function(route) {
        this.redirect = route.query && route.query.redirect;
      },
      immediate: true
    }
  },
  created() {
    this.getCookie();
  },
  methods: {
    getCookie() {
      const username = Cookies.get("username");
      const password = Cookies.get("password");
      const rememberMe = Cookies.get('rememberMe')
      this.loginForm = {
        username: username === undefined ? this.loginForm.username : username,
        password: password === undefined ? this.loginForm.password : decrypt(password),
        rememberMe: rememberMe === undefined ? false : Boolean(rememberMe)
      };
    },
    handleLogin() {
      console.log('in this')
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true;
          if (this.loginForm.rememberMe) {
            Cookies.set("username", this.loginForm.username, { expires: 30 });
            Cookies.set("password", encrypt(this.loginForm.password), { expires: 30 });
            Cookies.set('rememberMe', this.loginForm.rememberMe, { expires: 30 });
          } else {
            Cookies.remove("username");
            Cookies.remove("password");
            Cookies.remove('rememberMe');
          }
         console.log('运行到这里')
          this.$store.dispatch("Login", this.loginForm).then(() => {
            console.log('运行到这里2')
            this.$router.push({ path: this.redirect || "/" }).catch(()=>{});
          }).catch(() => {
            console.log('运行异常')
            this.loading = false;
            if (this.captchaOnOff) {
              this.getCode();
            }
          });
        }
      });
    }
  }
};
</script>


<style rel="stylesheet/scss" lang="scss">
.login {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
  background-image: url("../assets/images/login-background.jpg");
  background-size: cover;
}
.title {
  margin: 0px auto 30px auto;
  text-align: center;
  color: #707070;
}

.login-form {
  border-radius: 6px;
  background: #ffffff;
  width: 400px;
  padding: 25px 25px 5px 25px;
  .el-input {
    height: 38px;
    input {
      height: 38px;
    }
  }
  .input-icon {
    height: 39px;
    width: 14px;
    margin-left: 2px;
  }
}
.login-tip {
  font-size: 13px;
  text-align: center;
  color: #bfbfbf;
}
.login-code {
  width: 33%;
  height: 38px;
  float: right;
  img {
    cursor: pointer;
    vertical-align: middle;
  }
}
.el-login-footer {
  height: 40px;
  line-height: 40px;
  position: fixed;
  bottom: 0;
  width: 100%;
  text-align: center;
  color: #fff;
  font-family: Arial;
  font-size: 12px;
  letter-spacing: 1px;
}
.login-code-img {
  height: 38px;
}
</style>
