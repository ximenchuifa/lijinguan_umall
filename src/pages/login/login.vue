<template>
  <div class="login">
    <div class="container">
      <el-form label-width="80px" :model="form" ref="form" :rules="rules">
        <el-form-item label="用户名:" prop="username">
          <el-input v-model="form.username" clearable></el-input>
        </el-form-item>
        <el-form-item label="密码:" prop="password">
          <el-input v-model="form.password" clearable show-password></el-input>
        </el-form-item>
        <el-form-item class="btn">
          <el-button type="primary" @click="onSubmit('form')">登录</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import { successAlert, warningAlert } from "../../utils/alert";
import { reqLogin } from "../../utils/request";
import { validateNecessary } from "../../utils/validate";
export default {
  props: [],
  components: {},
  data() {
    return {
      form: {
        username: "",
        password: "",
      },
      rules: {
         username: [validateNecessary("请输入用户名")],
         password: [validateNecessary("请输入密码")],
      },
    };
  },
  computed: {
    ...mapGetters({}),
  },
  methods: {
    ...mapActions({ changeUserInfoAction: "changeUserInfoAction" }),
    onSubmit(form) {
      console.log(form);
      this.$refs[form].validate((valid) => {
        if (valid) {
          reqLogin(this.form).then((res) => {
            if (res.data.code == 200) {
              successAlert(res.data.msg);
              this.changeUserInfoAction(res.data.list);
              this.$router.push("/");
            } else {
              warningAlert(res.data.msg);
            }
          });
        }
      });
    },
  },
  mounted() {},
};
</script>
<style scoped>
.login {
  width: 100vw;
  height: 100vh;
  background: linear-gradient(to right, #553443, #303d60);
}
.container {
  width: 450px;
  height: 300px;
  position: relative;
  background-color: #fff;
  border: 1px solid #ccc;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
}
.el-form {
  margin: 80px 10px;
}
.btn {
  margin-top: 50px;
  text-align: center;
}
</style>