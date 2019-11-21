<template>
  <div>
    <!-- 头部 -->
    <header class="header">
      <img class="logo" src="https://www.51voa.com/images/voa.gif" alt="VOA美国之音" />
      <el-menu
        :default-active="activeIndex"
        class="el-menu-demo"
        mode="horizontal"
        @select="handleSelect"
      >
        <el-menu-item :index="item.id+''" v-for="(item,i) in tabs" :key="i">{{item.title}}</el-menu-item>
      </el-menu>
      <div class="btns">
        <el-button round v-if="!isLogIn" @click="showLoginBar">登录</el-button>
        <el-button round v-else @click="logout">退出</el-button>
      </div>
    </header>

    <router-view></router-view>

    <!-- 登陆框 -->
    <div>
      <el-dialog title="登录" width="30%" :visible.sync="loginVisible">
        <el-input
          v-model="user.name"
          class="user-name"
          clearable
          placeholder="请输入用户名"
          autocomplete="off"
        ></el-input>
        <el-input
          v-model="user.password"
          show-password
          clearable
          placeholder="请输入密码"
          type="password"
        ></el-input>
        <div slot="footer">
          <el-button @click="loginVisible=false;cancel()">取消</el-button>
          <el-button type="primary" @click="login">确定</el-button>
        </div>
      </el-dialog>
    </div>

    <!-- 退出框 -->
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      activeIndex: "1",
      tabs: [],
      lists: [],
      isLogIn: false,
      loginVisible: false,
      user: {
        name: "",
        password: ""
      }
    };
  },
  methods: {
    handleSelect(key) {
      this.$router.push({name:"home-list",params:{id:key}});
    },

    showLoginBar() {
      this.loginVisible = true;
    },
    showTip(title, message, type) {
      //提示信息
      this.$message({
        title,
        message,
        type
      });
    },
    logout() {
      this.$confirm("是否取消用户登录?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.isLogIn = false;
          this.showTip("成功", "用户已退出登录", "success");
        })
        .catch(() => {
          this.cancel();
        });
    },
    cancel() {
      this.$notify({
        type: "info",
        message: "已取消操作"
      });
    },
    getTabs() {
      axios
        .get("http://www.dell-lee.com/react/api/header.json")
        .then(result => {
          if (result.status === 200 && result.data.success) {
            this.tabs = result.data.data;
          } else {
            throw new Error("未获取到标题数据");
          }
        })
        .catch(err => {
          console.log(err);
        });
    },
    login() {
      var url;
      if (this.user.name && this.user.password) {
        url = `http://www.dell-lee.com/react/api/login.json?user=${this.user.name}&password=${this.user.password}`;
        axios
          .get(url)
          .then(result => {
            if (result.status === 200 && result.data.success) {
              //请求发送成功
              console.log(result.data);
              if (result.data.data.login) {
                this.isLogIn = true;
                this.loginVisible = false;
                this.showTip("成功", "登录成功", "success");
              } else {
                this.showTip("失败", "登录失败", "warning");
              }
            } else {
              throw new Error("查询失败");
            }
          })
          .catch(err => {
            if (err) {
              this.showTip("失败", "网络错误，请重试", "error");
            }
          });
      }
    }
  },
  created() {
    this.getTabs();
  }
};
</script>


<style lang="scss" scoped>
.header {
  background-color: #fff;
  display: flex;
  justify-content: space-around;
  align-items: center;
  .logo {
    height: 100%;
  }
}

.user-name {
  margin-bottom: 20px;
}
</style>