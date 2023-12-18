<template>
  <div class="user">
    <!-- 登录前 -->
    <div @click="onOpenModal" class="login-trigger" v-if="!isLogin">
      <i class="user-icon iconfont icon-yonghu" />
      <p class="user-name">未登录</p>
    </div>
    <!-- 登录后 -->
    <div @click="showUsrInfo" class="logined-user" v-else>
      <img v-lazy="$utils.genImgUrl(user.avatarUrl, 80)" class="avatar" />
      <p class="user-name">{{ user.nickname }}</p>
    </div>

    <!-- 登录框 -->
    <el-dialog
      :modal="false"
      :visible.sync="loginViewVisible"
      :width="$utils.toRem(320)"
    >
      <p slot="title">登录</p>
      <div class="login-body">
        <el-input
          class="input"
          placeholder="请输入用户名"
          v-model="uid"
        />
        <el-input
          class="pwdStyle"
          placeholder="请输入密码"
          v-model="pwd"
        />
        <div class="login-help" @click="openRegView">
          <p class="help">
            注册新账号
          </p>
        </div>
      </div>
      <span class="dialog-footer" slot="footer">
        <el-button
          :loading="loading"
          @click="onLogin(uid,pwd)"
          class="login-btn"
          type="primary"
          >登 录</el-button
        >
      </span>
    </el-dialog>
    <!-- 用户信息界面 -->
    <el-dialog
      :modal="false"
      :visible.sync="usrInfoViewVisible"
      :width="$utils.toRem(320)"
    >
      <p slot="title">个人信息</p>
      <div class="login-body">
        <el-input
          class="input"
          placeholder="用户名"
          v-model="userName"/>
          <el-input
          class="input"
          placeholder="密码"
          v-model="userDescribe"/>

        <el-button
          :loading="loading"
          @click="saveUsrInfo()"
          class="login-btn"
          type="primary"
          >保存</el-button>
      </div>
    </el-dialog>

    <!-- 用户注册界面 -->
    <el-dialog
      :modal="false"
      :visible.sync="regViewVisible"
      :width="$utils.toRem(320)"
    >
      <p slot="title">注册</p>
      <div class="login-body">
        <el-input
          class="input"
          placeholder="用户名"
          v-model="uid"/>
          <el-input
          class="pwdStyle"
          placeholder="密码"
          v-model="pwd"/>

        <el-button
          :loading="loading"
          @click="usrRegister(uid,pwd)"
          class="login-btn"
          type="primary"
          >注册</el-button>
      </div>
    </el-dialog>
  </div>

</template>

<script type="text/ecmascript-6">
import storage from "good-storage"
import { UID_KEY, isDef } from "@/utils"
import { confirm } from "@/base/confirm"
import {
  mapActions as mapUserActions,
  mapState as mapUserState,
  mapGetters as mapUserGetters
} from "@/store/helper/user"

export default {
  // 自动登录
  //当布局生成时
  created() {
    //可以添加自动登录
  },
  data() {
    return {
      regViewVisible:false,
      loginViewVisible: false,
      loading: false,
      usrInfoViewVisible: false,
      userDescribe:"",
      userName:"",
      uid: "",//返回userid
      pwd:""//返回password
    }
  },
  methods: {
    openRegView(){
      this.regViewVisible = true
      this.loginViewVisible = false
    },
    onOpenModal() {
      this.loginViewVisible = true
    },
    onCloseModal() {
      this.loginViewVisible = false
    },
    async onLogin(uid,pwd) {//登陆按钮被单击时
      this.loading = true
      console.log("Hi")
      //登陆是否成功
      const success = await this.login(uid).finally(() => {
        this.loading = false
      })
      if (success) {
        this.onCloseModal()
      }
    },
    //当处于登录状态按钮被单击时，弹出用户信息界面
    showUsrInfo() {
      this.usrInfoViewVisible = true;
    },
    //保存用户信息
    saveUsrInfo(){
      confirm("确定要保存修改吗？", () => {
        //添加保存用户信息的内容
        this.usrInfoViewVisible = false;
      });
    },
    //注册
    usrRegister(uid,pwd){
      console.log("uid: "+uid,"pwd: "+pwd);
      this.regViewVisible = false;
      this.loginViewVisible = true;
    },
    ...mapUserActions(["login", "logout"])
  },
  computed: {
    ...mapUserState(["user"]),
    ...mapUserGetters(["isLogin"])
  },
  components: {}
}
</script>

<style lang="scss" scoped>
.user {
  padding: 16px;
  padding-bottom: 0;
  margin-bottom: 12px;

  .login-trigger {
    display: flex;
    align-items: center;
    cursor: pointer;
  }
  .user-icon {
    font-size: 24px;
  }

  .user-name {
    margin-left: 8px;
  }

  .logout {
    transform: translateY(1px);
    margin-left: 8px;
    color: var(--font-color-shallow-grey);
  }

  .login-body {
    .input {
      margin-bottom: 16px;
    }
    .pwdStyle {
      -webkit-text-security: disc; /* Safari/Chrome */
      margin-bottom: 16px;
    }
    .login-help {
      .help {
        margin-bottom: 4px;
        color: blue;
      }
    }
  }

  .login-btn {
    width: 100%;
    padding: 8px 0;
  }

  .logined-user {
    display: flex;
    align-items: center;
    cursor: pointer;

    .avatar {
      @include round(40px);
    }
  }
}
</style>
