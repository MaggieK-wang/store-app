<template>
  <div class="container">
    <div class="header">
      <!-- 顶部导航栏 -->
      <van-nav-bar title="我的订单" left-text="返回" left-arrow @click-left="onClickLeft" />
    </div>
    <div class="order-wrapper" ref="order">
      <van-tabs v-model="active">
        <van-tab title="全部订单">
          <ul class="wrapper">
            <li class="order-item" :key="index" v-for="(order, index) in list.list">
              <div class="order-item-header">
                <i class="iconfont iconwodedingdan"></i>
                <p>{{ order.orderCode }}</p>
                <span>{{ order.skuName }}</span>
                <span class="payStatusName">{{ order.payStatusName }}</span>
              </div>
              <ul>
                <li :key="index" v-for="(goods, index) in order.orderDetails">
                  <div class="order-content">
                    <div class="icon">
                      <img alt :src="goods.images[0]" />
                    </div>
                    <div class="content">
                      <div class="skuName">{{ goods.skuName }}</div>

                      <div class="createdTime">创建时间：{{ goods.createdTime | datetimeFilter }}</div>
                      <div class="salePrice">
                        <span class="num">×{{ goods.shopNum }}</span>
                        <span class="price">¥{{ goods.salePrice }}</span>
                      </div>
                      <div class="info">
                        <p>
                          共{{ goods.shopNum }}件商品,合计
                          <span>¥{{ goods.skuAmount }}</span>
                        </p>
                      </div>
                    </div>
                  </div>
                </li>
              </ul>
            </li>
          </ul>
        </van-tab>
        <van-tab title="已付款"></van-tab>
        <van-tab title="已完成"></van-tab>
        <van-tab title="已取消"></van-tab>
      </van-tabs>
    </div>
  </div>
</template>

<script>
import { getOrderList } from "@/api";
import BScroll from "better-scroll";
import moment from "moment";
// import { List, NavBar, Toast } from "vant";
export default {
  name: "order",
  data() {
    return {
      // 默认选中标签页
      active: 0,
      list: [],
      orderQueryList: {
        loginAccount: "",
        pageNum: 1,
        pageSize: 20
      }
    };
  },

  filters: {
    //时间格式化
    datetimeFilter(datetime) {
      return moment(datetime).format("YYYY-MM-DD");
    }
  },
  created() {
    this.getList();
    this._ininScroll();
  },
  methods: {
    getList() {
      this.orderQueryList.loginAccount = this.$store.state.userInfo.userAccount;
      getOrderList(this.orderQueryList).then(response => {
        this.list = response.data;
        console.log("222");
        console.log(this.list);
        //debugger
      });
    },
    _ininScroll() {
      this.$nextTick(() => {
        this.orderScroll = new BScroll(this.$refs.order, {
          click: true,
          scrollY: true
        });
      });
    },
    // 返回按钮
    onClickLeft() {
      this.$router.replace("/home/user");
    }
    // onClickRight() {
    //   Toast("按钮");
    // },
  }
};
</script>

<style lang="scss" scoped>
.goods-card {
  margin: 0rem;
  background-color: white;
}

.delete-button {
  height: 100%;
}
.order-wrapper {
  position: absolute;
  top: 45px;
  // height: 80%;
  left: 0;
  right: 0;
  bottom: 0;
  overflow: hidden;
  ul {
    height: auto;
    .order-item {
      display: flex;
      flex-direction: column;
      padding: 18px;
      margin: 18px;
      border-bottom: 1px solid #cccccc;
      border-radius: 4px;
      // background: whitesmoke;
      &.last-child {
        border: none;
        margin-bottom: 0;
      }
      .order-item-header {
        height: 1rem;
        line-height: 1rem;
        i {
          float: left;
          color: #d2b189;
        }
        p {
          float: left;
        }
        span {
          float: right;
          color: black;
        }
        .payStatusName {
          background: skyblue;
          border-radius: 4px;
          position: absolute;
          margin-left: 45px;
        }
      }
      .order-content {
        display: flex;
        .icon {
          flex-basis: 70px;
          margin: 0.75rem;
          img {
            width: 90px;
            height: 125px;
          }
        }
        .content {
          position: relative;
          flex: 1;
          text-align: left;
          .skuName {
            font-size: 1rem;
            font-weight: normal;
            height: 1rem;
            line-height: 1rem;
            margin: 10px 0;
            overflow: hidden;
          }
          .createdTime {
            height: 2rem;
            overflow: hidden;
            margin: 10px 0;
          }
          .salePrice {
            height: 1rem;
            line-height: 1rem;
            .num {
              color: rgb(209, 199, 199);
              font-size: 0.5rem;
              float: right;
            }
            .price {
              color: red;
              font-size: 1rem;
              float: right;
            }
          }
          .info {
            float: right;
            margin-top: 5px;
            span {
              font-size: 1rem;
            }
          }
        }
      }
    }
  }
}
</style>
