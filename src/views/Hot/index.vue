<template>
  <v-touch v-on:swipeleft="swipeleft" class="hot">
    <div class="hot">
      <AppHeader title="首页" />

      <div ref="hot" class="hot1">
        <div class="scroll">
          <div class="banner" v-if="bannerList">
            <!--轮播插件-->
            <AppSwipper>
              <div :key="index" v-for="(item, index) in bannerList" :bannerList="bannerList">
                <img :src="item.picUrl" />
              </div>
            </AppSwipper>
          </div>
          <!--下方列表-->
          <div class="good-list">
            <div class="title">热门商品</div>
            <div class="container">
              <div
                class="card"
                v-for="(good, index) in goodList"
                :key="index"
                @click="handleGoodDetail(good)"
              >
                <img :src="good.images[0]" alt />
                <div class="name">{{ good.skuName }}</div>
                <div class="price">
                  <span class="new">￥{{ good.salePrice }}</span>
                  <span class="old">￥{{ good.costPrice }}</span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!--商品详情-->
      <GoodDetail :visible.sync="GoodDetailVisible" :good="currentgood" />
    </div>
  </v-touch>
</template>

<script>
import { getBanner, getGoods, getGoodbySkuCode } from "@/api";
import AppSwipper from "@/components/AppSwipper";
import GoodDetail from "@/components/GoodDetail";
import AppHeader from "@/components/AppHeader";
import BScroll from "better-scroll";
export default {
  name: "hot",
  components: {
    AppSwipper,
    GoodDetail,
    AppHeader
  },
  data() {
    return {
      bannerList: [],
      bannerQueryList: {
        picStatus: "1",
        pageNum: "1",
        pageSize: "100"
      },
      goodsQueryList: {
        picStatus: "1",
        pageNum: "1",
        pageSize: "20"
      },
      goodList: [],
      scrollY: 0,
      // 商品详情
      GoodDetailVisible: false,
      currentgood: undefined
    };
  },
  created() {
    //获取轮播图
    this.getBannerData();
    //获取商品数据
    this.getGoodsData();
    //启动商品列表滚动
    this._initScroll();
  },
  methods: {
    getBannerData() {
      getBanner(this.bannerQueryList).then(response => {
        console.log(response.data);
        this.bannerList = response.data.list;
      });
    },

    swipeleft: function() {
      this.$router.push({ path: "/home/cate" });
    },
    getGoodsData() {
      getGoods(this.goodsQueryList).then(response => {
        //console.log(response.data);
        this.goodList = response.data.list;
        console.log(this.goodList);
        // debugger;
      });
    },
    _initScroll() {
      this.$nextTick(() => {
        this.glScroll = new BScroll(this.$refs.hot, {
          click: true,
          scrollY: true
        });
      });
    },
    handleGoodDetail(good) {
      getGoodbySkuCode({ skuCode: good.skuCode }).then(response => {
        console.log(response.data);
        this.currentgood = response.data;
        this.GoodDetailVisible = true;
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.hot {
  position: absolute;
  top: 24px;
  left: 0px;
  right: 0px;
  bottom: 26px;
  overflow: hidden;

  .hot1 {
    position: absolute;
    top: 0px;
    left: 0px;
    right: 0px;
    bottom: 0px;
    overflow: hidden;
  }

  .banner {
    width: 100%;
  }
  .good-list {
    background: rgba($color: #eee, $alpha: 1);
    .title {
      text-align: center;
      font-size: 1rem;
      height: 2rem;
      line-height: 2rem;
      background: #ecc89c;
    }
    .container {
      box-sizing: border-box;
      display: flex;
      flex-wrap: wrap;
      justify-content: space-around;
      .card {
        padding: 0.5rem 0;
        // border-radius: 10px;
        background: #fff;
        width: 150px;
        height: 160px;
        display: flex;
        flex-direction: column;
        align-items: center;
        margin-bottom: 1rem;
        justify-content: space-between;
        // border-radius: 15px;
        img {
          width: 70px;
          height: 100px;
        }

        .price {
          margin-top: 5px;

          font-size: 16px;
          position: relative;

          .now {
            margin-right: 10px;
            font-size: 1rem;
            color: red;
          }
          .old {
            margin-left: 16px;
            font-size: 0.8rem;
            color: #ccc;
            text-decoration: line-through;
          }
        }
      }
    }
  }
}
</style>
