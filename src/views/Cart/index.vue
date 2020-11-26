<template>
  <v-touch v-on:swipeleft="swipeleft" v-on:swiperight="swiperight" class="cart">
    <div class="cart">
      <AppHeader title="购物车" />
      <!--列表-->
      <div class="goods-warpper" ref="cart">
        <ul class>
          <li :key="index" class="good-item" v-for="(good, index) in list">
            <div class="icon">
              <img :src="good.images[0]" alt />
            </div>
            <div class="content">
              <h2 class="name">{{ good.skuName }}</h2>
              <div class="datetime">时间:2011/11/11 22:22</div>
              <div class="price">
                <span class="now">￥{{ good.salePrice }}</span>
              </div>
              <div class="control">
                <CartControl :good="good" />
              </div>
            </div>
          </li>
        </ul>
      </div>
      <!--购物车结算组件-->
      <AccountBar :selectGoods="selectGoods" />
    </div>
  </v-touch>
</template>

<script>
import BScroll from "better-scroll";
import { getCart } from "@/api";
import CartControl from "@/components/CartControl";
import AccountBar from "@/components/AccountBar";
import AppHeader from "@/components/AppHeader";
export default {
  components: {
    CartControl,
    AccountBar,
    AppHeader
  },
  data() {
    return {
      cartQueryList: {
        // userId:46,
        pageSize: 10,
        pageNum: 1
      },
      list: []
    };
  },
  created() {
    this.getList();
  },
  methods: {
    swipeleft: function() {
      this.$router.push({ path: "/home/user" });
    },
    swiperight: function() {
      this.$router.push({ path: "/home/cate" });
    },
    getList() {
      var _parma = {
        /*userId:this.cartQueryList.userId,
        pageSize:this.cartQueryList.pageSize,
        pageNum:this.cartQueryList.pageNum*/
      };
      // this.cartQueryList.userId = this.$store.state.userInfo.userId
      getCart(_parma).then(response => {
        //console.log("购物车列表响应信息");
        //console.log(response);
        //debugge
        this.list = response.data;
        this._initScroll();
      });
    },

    _initScroll() {
      this.$nextTick(() => {
        this.cartScroll = new BScroll(this.$refs.cart, {
          click: true
        });
      });
    }
  },
  computed: {
    selectGoods() {
      var _list = [];
      this.list.forEach(v => {
        if (v.count > 0) {
          _list.push({
            ...v,
            price: v.salePrice,
            userId: this.cartQueryList.userId,
            shopNums: v.count
          });
        }
      });

      return _list;
    }
  }
};
</script>

<style lang="scss" scoped>
.cart {
  position: absolute;
  top: 26px;
  left: 0px;
  right: 0px;
  bottom: 25px;
  overflow: hidden;
  display: flex;
  .goods-warpper {
    width: 100%;
  }
}
.good-item {
  display: flex;
  padding-bottom: 18px;
  margin: 18px;
  border-radius: 5px;
  background: whitesmoke;
  &:last-child {
    border: none;
    margin-bottom: 0;
  }
  .icon {
    flex-basis: 70px;
    margin-right: 0.75rem;
    img {
      width: 80px;
      height: 120px;
    }
  }
  .content {
    position: relative;
    flex: 1;
    text-align: left;
    .name {
      font-size: 1rem;
    }
    .now {
      color: red;
      font-size: 15px;
    }
    .control {
      position: absolute;
      bottom: 0;
      right: 0;
    }
  }
}
</style>
