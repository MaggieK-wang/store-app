<template>
  <v-touch v-on:swiperight="swiperight" class="app-user">
    <div class="app-user">
      <AppHeader title="个人中心" />
      <div class="content">
        <!-- 修改密码 -->

        <div class="upPwd" v-show="pwdVisible">
          <div class="pwdContent">
            <div class="title">
              修改密码
              <span @click="handleBack">
                <i class="iconfont iconxiayige"></i>
              </span>
            </div>
            <form class="pwdForm">
              <div class="item">
                <span>原始密码:</span>
                <input type="text" v-model="updatePwdById.originPwd" />
              </div>
              <div class="item">
                <span>新密码 :</span>
                <input type="text" v-model="updatePwdById.newPwd" />
              </div>
              <div class="item" style>
                <span>确认密码:</span>
                <!-- <label for="确认密码"></label> -->
                <input type="password" v-model="renewPwd" />
              </div>
              <button class="button" @click="upPwdMethods">提交</button>
            </form>
          </div>
        </div>
        <!-- 修改积分 -->
        <div class="upPwd" v-show="scoreVisible">
          <div class="pwdContent">
            <div class="title">
              充值积分
              <span @click="handleBack">
                <i class="iconfont iconxiayige"></i>
              </span>
            </div>
            <div class="pwdForm">
              <div class="item">
                <span>充值余额:</span>
                <input type="text" v-model="updateScoreForm.newScore" />
              </div>
              <button class="button" @click="upScorePwdMethods" style="margin-top:30px">充值</button>
            </div>
          </div>
        </div>
        <!-- 确认是否删除账号 -->
        <div class="upPwd" v-show="deDialog">
          <div class="pwdContent">
            <div class="title">
              是否删除账号
              <span @click="handleBack">
                <i class="iconfont icon-guanbi"></i>
              </span>
            </div>
            <div class="pwdForm">
              <button class="button" @click="handedeleteCustomer" style="margin-top:30px">确认</button>
              <button class="button" @click="handleBack" style="margin-top:30px">取消</button>
            </div>
          </div>
        </div>

        <!--修改信息表单TODO:-->
        <div class="upPwd" v-show="updateVisible">
          <div class="pwdContent">
            <div class="title">
              修改用户信息
              <span @click="handleBack">
                <i class="iconfont iconxiayige"></i>
              </span>
            </div>
            <div class="pwdForm">
              <div class="item">
                <span class="iconfont iconpersonal"></span>
                <span>
                  <input type="text" placeholder="请输入更新人" v-model="updateFormData.updatedBy" />
                </span>
              </div>
              <div class="item">
                <span class="iconfont iconshoujihao"></span>

                <span>
                  <input type="text" placeholder="请输入手机号" v-model="updateFormData.phone" />
                </span>
              </div>
              <div class="item">
                <span class="iconfont iconA"></span>
                <span>
                  <input type="text" placeholder="请输入姓名" v-model="updateFormData.userName" />
                </span>
              </div>
              <button class="button" @click="updateCoustomer">修改</button>
            </div>
          </div>
        </div>
        <!-- 页面 -->
        <div>
          <!-- user页面 -->
          <div class="logo">
            <img src="@/assets/header.jpg" alt />
            <span>{{userInfo.userName}}</span>
          </div>
          <div class="informationPart">
            <span>当前账号:{{userInfo.userAccount}}</span>
            <span class="second">积分数:{{userInfo.score}}</span>
          </div>
          <div class="container">
            <div class="item" @click="orderVisibleChange">
              <div>
                <i class="iconfont iconwodedingdan"></i>
                <span>我的订单</span>
              </div>
              <i class="iconfont iconxiayige"></i>
            </div>
            <div class="item" @click="userUpdatePwd">
              <div>
                <i class="iconfont iconmima_huaban1"></i>
                <span>修改密码</span>
              </div>
              <i class="iconfont iconxiayige"></i>
            </div>
            <div class="item" @click="userUpdatScore">
              <div>
                <i class="iconfont iconico"></i>
                <span>充值积分</span>
              </div>
              <i class="iconfont iconxiayige"></i>
            </div>

            <div class="item" @click="updateUser">
              <div>
                <i class="iconfont iconmima_huaban1"></i>
                <span>修改用户信息</span>
              </div>
              <i class="iconfont iconxiayige"></i>
            </div>

            <div class="item" @click="deDialog = true">
              <div>
                <i class="iconfont icontuichudenglu"></i>
                <span>注销账户</span>
              </div>
              <i class="iconfont iconxiayige"></i>
            </div>
            <div class="item" @click="handleOut">
              <div>
                <i class="iconfont icontuichudenglu"></i>

                <span>退出登录</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </v-touch>
</template>

<script>
var loginAccount = JSON.parse(sessionStorage.getItem("loginAccount"));
import {
  loginOut,
  updatePwd,
  updateScore,
  deleteCustomer,
  getCustomer
} from "@/api";
import AppHeader from "@/components/AppHeader";

export default {
  name: "user",
  components: {
    AppHeader
  },
  data() {
    return {
      //默认关闭
      updateVisible: false,
      userInfo: "",
      orderVisible: false,
      pwdVisible: false,
      deDialog: false,
      scoreVisible: false,
      renewPwd: "",
      list: [],
      updatePwdById: {
        originPwd: undefined,
        newPwd: undefined
      },
      updateScoreForm: {
        newScore: ""
      },
      //用户修改TODO:
      updateFormData: {
        phone: "",
        userName: "",
        updatedBy: ""
      }
    };
  },
  created() {
    this.getUserDetail();
  },
  methods: {
    swiperight: function() {
      this.$router.push({ path: "/home/cart" });
    },
    userUpdatePwd() {
      this.pwdVisible = true;
    },
    userUpdatScore() {
      this.scoreVisible = true;
    },
    //我的订单
    //我的订单
    orderVisibleChange() {
      this.$router.push({ path: "/order" });
    },
    handleBack() {
      this.orderVisible = false;
      this.updateVisible = false;
      this.scoreVisible = false;
      this.deDialog = false;
      this.pwdVisible = false;
      this.updatePwdById = {
        originPwd: undefined,
        newPwd: undefined
      };
      this.updateScoreForm = {
        newScore: ""
      };
    },
    updateUser() {
      this.updateVisible = true;
    },
    // 获取用户详情
    getUserDetail() {
      getCustomer().then(response => {
        this.userInfo = response.data;
      });
    },

    // 修改密码
    upPwdMethods() {
      if (this.renewPwd == this.updatePwdById.newPwd) {
        updatePwd(this.updatePwdById).then(response => {
          this.updateVisible = false;
          this.updatePwdById = {
            originPwd: undefined,
            newPwd: undefined
          };

          this.$layer.toast({
            icon: "", // 图标clssName 如果为空 toast位置位于下方,否则居中
            content: "修改成功",
            time: 2000 // 自动消失时间 toast类型默认消失时间为2000毫秒
          });
        });
      } else {
        this.$layer.toast({
          icon: "", // 图标clssName 如果为空 toast位置位于下方,否则居中
          content: "两次密码不一致，修改失败",
          time: 2000 // 自动消失时间 toast类型默认消失时间为2000毫秒
        });
      }
    },
    // 修改积分
    upScorePwdMethods() {
      updateScore(this.updateScoreForm).then(response => {
        // console.log(response.msg);
        // debugger
        this.updateScoreForm = {
          newScore: ""
        };
        this.$layer.toast({
          icon: "", // 图标clssName 如果为空 toast位置位于下方,否则居中
          content: "充值成功",
          time: 2000 // 自动消失时间 toast类型默认消失时间为2000毫秒
        });
        this.scoreVisible = false;
        this.getUserDetail();
      });
      // this.userInfo.score = this.updateScoreForm.newScore + userInfo.score;
    },
    //用户修改TODO:
    updateCoustomer() {
      updateCustomer(this.updateFormData).then(response => {
        console.log(response);
        // debugger
      });
      this.$layer.open({
        style: "border:none; background-color:#78BA32; color:#fff;",
        content: response.msg
      });
    },
    // 注销账户
    handedeleteCustomer() {
      deleteCustomer().then(response => {
        this.$layer.toast({
          icon: "", // 图标clssName 如果为空 toast位置位于下方,否则居中
          content: "注销成功",
          time: 2000 // 自动消失时间 toast类型默认消失时间为2000毫秒
        });
      });
    },
    //退出登录
    handleOut() {
      loginOut({ userAccount: loginAccount }).then(response => {
        this.$layer.open({
          style: "border:none; background-color:#78BA32; color:#fff;",
          content: response.msg
        });
        this.$router.push({ path: "/" });
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.app-user {
  position: relative;
  .informationPart {
    font-size: 1rem;
    width: 100%;
    display: flex;
    position: absolute;
    top: 180px;
    left: 10px;
    .second {
      margin-left: 60px;
    }
    span {
      display: inline-block;
    }
  }
  height: 100%;
  // background: blanchedalmond;
  .order-warpper {
    position: fixed;
    top: 0;
    bottom: 0;
    width: 100%;
    left: 0;
    z-index: 188;
    background: #fff;
    .header {
      width: 100%;
      display: flex;
      // justify-content: space-between;
      .left {
        border-radius: 50%;
        color: rgb(201, 156, 106);
        i {
          font-size: 1.7rem;
        }
      }
      .center {
        height: 2rem;
        line-height: 2rem;
        margin-left: 120px;
        h2 {
          color: #c39862;
          font-size: 1.3rem;
          padding-top: 0.3rem;
        }
      }
    }
    .menu-warpper {
      display: flex;
      flex-direction: row;
      justify-content: space-between;
      padding: 10px;
      a {
        text-decoration: none;
        font-size: 1rem;
        color: rgb(94, 91, 91);
        padding: 10px;
        a:hover {
          background: rgb(173, 106, 81);
          color: rgb(173, 106, 81);
        }
      }
    }
  }
  .logo {
    display: flex;
    //border: 1px solid red;
    width: 200px;
    height: 200px;
    img {
      position: absolute;
      left: 40px;
      top: 60px;
      width: 20%;
      height: 40%;
      border-radius: 50%;
    }
    span {
      display: inline-block;
      position: absolute;
      left: 100px;
      top: 80px;
      width: 50%;
      // height: 10%;
      font-size: 25px;
    }
  }
  .container {
    border-radius: 10px;
    background: rgb(250, 245, 235);
    // height: 29%;
    box-sizing: border-box;
    width: 95%;
    margin: 10px;
    //border: 1px solid blue;
    position: absolute;
    top: 200px;

    font-weight: bold;
    padding-top: 10px;
    .item {
      display: flex;
      justify-content: space-between;
      width: 80%;
      //border: 1px red solid;
      padding: 10px 35px;

      border-radius: 8px;
      span {
        display: inline-block;
        margin-left: 0.3rem;
        font-size: 1rem;
        line-height: 0.5rem;
        color: rgb(78, 76, 76);
      }
      i {
        color: rgb(173, 106, 81);
        display: inline-block;
        font-weight: bold;
      }
    }
  }
  .upPwd {
    width: 100%;
    .pwdContent {
      position: fixed;
      z-index: 2;
      width: 80%;
      height: 14rem;
      border: #c39862 1px solid;
      border-radius: 6px;
      left: 37px;
      top: 300px;
      background: #eee;
      margin: auto 0;
      display: flex;
      flex-direction: column;
      .title {
        font-size: 1.5rem;
        span {
          float: right;
          // margin-top: -0.8rem;
          .icon-weibiaoti34 {
            font-size: 1.5rem;
          }
        }
      }
      .pwdForm {
        margin-top: 3%;
        .item {
          margin: 0.8rem 2.4rem;
          // float: right;
          font-size: 0.8rem;
          span {
            width: 100px;
            text-align: right;
          }
          input {
            height: 1.5rem;
            line-height: 1.2rem;
            border: none;
            // text-align: right;
          }
        }
        .button {
          width: 30%;
          font-size: 0.8rem;
          background: #eee;
          // border: none;
        }
      }
    }
  }
}
</style>
