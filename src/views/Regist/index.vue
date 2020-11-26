<template>
  <div class="login">
    <AppHeader title="注册" />
    <!-- <i class="iconfront iconfanhui"></i> -->

    <div class="register-warpper">
      <div>
        <form action>
          <div class="form">
            <div class="logo">
              <img src="@/assets/logo.png" alt srcset />
            </div>
            <div class="gender">
              <div class="bg-gender">
                <div
                  class="gender-icon-nan"
                  :class="{ 'current-gender-icon': currentGender == 'boy' }"
                  @click="genderChange('boy')"
                >
                  <i class="iconfont icongender-men" />
                </div>
                <div
                  class="gender-icon-nv"
                  :class="{ 'current-gender-icon': currentGender == 'girl' }"
                  @click="genderChange('girl')"
                >
                  <i class="iconfont icongender-women" />
                </div>
              </div>
              <div class="font-gender">
                <i :class="{ 'current-gender': currentGender == 'boy' }">男</i>
                <i :class="{ 'current-gender': currentGender == 'girl' }">女</i>
              </div>
            </div>
            <div class="content">
              <div class="form-item">
                <span>
                  <i class="iconfont iconA"></i>
                </span>
                <input type="text" name id placeholder="请输入用户账户" v-model="formData.userAccount" />
              </div>
              <div class="form-item">
                <span>
                  <i class="iconfont iconpersonal"></i>
                </span>
                <input type="text" name id placeholder="请输入用户名" v-model="formData.userName" />
              </div>

              <div class="form-item">
                <span>
                  <i class="iconfont iconmima"></i>
                </span>
                <input type="password" placeholder="请输入密码" v-model="formData.password" />
              </div>
              <div class="form-item">
                <span>
                  <i class="iconfont iconquerenmima"></i>
                </span>
                <input
                  type="password"
                  placeholder="请确认密码"
                  ref="password"
                  v-model="formData.repeatpwd"
                />
              </div>
              <div class="form-item">
                <span>
                  <i class="iconfont iconshoujihao"></i>
                </span>
                <input type="text" placeholder="请输入手机号" v-model="formData.phone" />
              </div>
              <!-- <div class="form-item">
								<span>
									<i class="iconfont iconfaxian"></i>
								</span>
								<input
									type="text"
									placeholder="请输入前台积分"
									v-model="formData.frontScore"
								/>
              </div>-->

              <div class="form-item">
                <span>
                  <i class="iconfont iconcredentials_icon"></i>
                </span>
                <input type="text" placeholder="请输入身份证号" v-model="formData.idCard" />
              </div>
            </div>
          </div>
        </form>
      </div>
      <div class="submit" @click="addCustomer">注册</div>
      <div class="back">
        <a href @click="change('/')">返回</a>
      </div>
    </div>
  </div>
</template>

<script>
import { addCustomer } from "@/api";
import AppHeader from "@/components/AppHeader";
export default {
  name: "Regist",
  components: {
    AppHeader
  },
  data() {
    return {
      formData: {
        userAccount: "",
        userName: "",
        userSex: "",
        phone: "",
        email: "",
        idCard: "",
        password: "",
        frontScore: 500,
        loginAccount: "",
        isAdmin: "0",
        repeatpwd: ""
      },
      tStyle: {
        "font-size": "40px",
        color: "white",
        "font-weight": "bold",
        "text-align": "center"
        // "margin-top": "20px"
      }
    };
  },
  created() {
    this.genderChange(this.currentGender);
  },
  methods: {
    change(url) {
      // 页面跳转方法
      this.$router.push(url);
    },

    addCustomer() {
      addCustomer(this.formData).then(response => {
        if (this.formData.userAccount === "") {
          this.$layer.toast({
            icon: "", // 图标clssName 如果为空 toast位置位于下方,否则居中
            content: "用户名不能空",
            time: 2000 // 自动消失时间 toast类型默认消失时间为2000毫秒
          });
          return;
        }
        if (this.formData.password !== this.formData.repeatpwd) {
          this.$layer.toast({
            icon: "", // 图标clssName 如果为空 toast位置位于下方,否则居中
            content: "密码不一致",
            time: 2000 // 自动消失时间 toast类型默认消失时间为2000毫秒
          });
          return;
        }
        // this.$layer.toast({
        //   icon: "", // 图标clssName 如果为空 toast位置位于下方,否则居中
        //   content: "注册成功",
        //   time: 2000 // 自动消失时间 toast类型默认消失时间为2000毫秒
        // });
        this.$layer.open({
          style: "border:none; background-color:#78BA32; color:#fff;",
          content: "注册成功"
        });
      });
    },
    genderChange(sex) {
      this.currentGender = sex;
      if (sex == "boy") {
        this.formData.userSex = "1";
      } else {
        this.formData.userSex = "0";
      }
    }
  }
};
</script>
<style lang="scss" scoped>
.login {
  height: 100%;
  padding: 2rem;

  .form {
    .form-item {
      display: inline-block;
      justify-content: space-between;
      border-bottom: solid 1px rgba($color: #000000, $alpha: 0.3);
      margin: 0 20px 2rem 20px;
      padding: 0 20px 0.5rem 20px;
      i {
        color: coral;
        font-weight: bold;
        margin-right: 0.8rem;
      }
      input {
        flex: 1;
        height: 1.5rem;
        line-height: 1.5rem;
        margin-right: 1rem;
        border: none;
      }
    }
  }
}
.register-warpper {
  position: fixed;
  top: 0;
  bottom: 0;
  width: 100%;
  left: 0;
  z-index: 188;
  background: #fff;

  .form {
    margin-top: 20%;
    width: 100%;
    height: 80%;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    .logo {
      width: 100%;
      img {
        //图片自适应
        width: 60%;
        height: 20%;
        max-width: 100%;
        max-height: 100%;
        // flex: -3;
      }
    }
    .gender {
      width: 100%;
      margin-top: 60px;
      text-align: center;
      .bg-gender {
        width: 10.4rem;
        margin: 0 auto;
        background: #eee;
      }
      .gender-icon-nan {
        display: inline-block;
        width: 5.2rem;
        height: 2rem;
        // background: #eee;
        .icongender-men {
          color: #fff;
          font-size: 1.5rem;
          font-weight: bolder;
        }
      }
      .gender-icon-nv {
        display: inline-block;
        width: 5.2rem;
        height: 2rem;
        // background: #eee;
        .icongender-women {
          color: #fff;
          font-size: 1.5rem;
          font-weight: bolder;
        }
      }
      .font-gender {
        margin-top: 10px;
        i {
          margin: 0 2rem;
          font-size: 0.8rem;
        }
      }
    }
  }
  .content {
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: 10px;
    margin-bottom: 20px;
    .form-item {
      margin: 18px 0 0 0px;
      width: 63%;
      border-bottom: 1px solid #eee;
      .iconfont {
        color: #e6bf8e;
        font-size: 1rem;
        font-weight: bold;
        margin-right: 1.5rem;
      }
      input {
        border: none;
        font-size: 0.8rem;
      }
    }
  }
}
.submit {
  // margin: 60px 10px 20px;
  background: linear-gradient(to right, #e6bf8e, #cea570);
  font-size: 20px;
  color: #ffff;
  font-weight: bolder;
  border-radius: 10px;
  padding: 10px;
  width: 70%;
  margin: auto;
}

a {
  color: darkgray;
  text-decoration: none;
}
.back {
  text-align: center;
  margin-top: 20px;
}

.current-gender {
  color: rgb(201, 156, 106);
}
.current-gender-icon {
  background: rgb(201, 156, 106);
}
</style>
