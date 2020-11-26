<template>
  <v-touch v-on:swipeleft="swipeleft" v-on:swiperight="swiperight" class="goods">
    <div class="goods">
      <AppHeader title="商品分类" />
      <div class="meun-warpper" ref="mw">
        <ul>
          <li
            :key="index"
            v-for="(item, index) in goods"
            class="meun-item"
            :class="{'current': currentIndex === index }"
            @click="handleSelect(index, $event)"
          >
            <span class="text">{{ item.cateName }}</span>
          </li>
        </ul>
      </div>
      <div class="goods-warpper" ref="gw">
        <ul>
          <li :key="index" v-for="(item, index) in goods" class="goods-list" ref="goodList">
            <h1 class="title">{{ item.cateName }}</h1>
            <ul class>
              <div v-if="item.goods.length === 0">
                <span>此分类下没有上架得书籍，敬请期待</span>
              </div>
              <li
                :key="index"
                v-for="(good, index) in item.goods"
                class="goods-item"
                @click="handleGoodDetail(good,$event)"
              >
                <!-- 左侧 -->
                <div class="icon">
                  <img :src="good.images[0]" alt />
                </div>
                <!-- 右侧 -->
                <div class="content">
                  <h2 class="name">{{ good.skuName }}</h2>

                  <div class="desc">{{ good.skuIntroduction }}</div>
                  <div class="price">
                    <span class="now">￥{{ good.salePrice }}</span>

                    <span class="old">￥{{ good.costPrice }}</span>
                    <span class="saleNum">销量：{{ good.saleNum }}</span>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <!-- 商品详情 -->
      <GoodDetail :visible.sync="GoodDetailVisible" :good="currentgood" />
    </div>
  </v-touch>
</template>

<script>
import { getCate, getGoods, getGoodbySkuCode } from "@/api";
import GoodDetail from "@/components/GoodDetail";
import BScroll from "better-scroll";
import AppHeader from "@/components/AppHeader";
export default {
  components: {
    AppHeader,
    GoodDetail
  },
  name: "cate",
  data() {
    return {
      // 处理后的数据格式
      goods: [],

      goodsQueryList: {
        pageNum: "1",
        pageSize: "100"
      },
      // 商品类别数据备份（保留一份原始数据结构，以备不时之需）
      cateList: [],
      goodList: [],
      //类别索引
      //   currentIndex: 0,
      scrollY: 0,
      listHeight: [],
      // 商品详情
      GoodDetailVisible: false,
      currentgood: undefined
    };
  },
  computed: {
    //动态计算商品类别
    currentIndex() {
      for (var i = 0; i < this.listHeight.length; i++) {
        var h1 = this.listHeight[i];
        var h2 = this.listHeight[i + 1];

        if (!h2 || (this.scrollY >= h1 && this.scrollY < h2)) {
          return i;
        }
      }
      return 0;
    }
  },
  created() {
    // 对数据进行格式处理
    this._initData();
    // this._otherinitData();
    this._initScroll();
  },

  methods: {
    swipeleft: function() {
      this.$router.push({ path: "/home/cart" });
    },
    swiperight: function() {
      this.$router.push({ path: "/home" });
    },
    //只要有类别就展示出来
    _initData() {
      getCate()
        .then(response => {
          console.log(response);
          // 先保留一份原始数据结构，以备不时之需
          this.cateList = response.data;
          // 数组不断地去循环每一个数组，不断的添加之前实用的变量用reduce
          var A_cateList = response.data.reduce((acc, cur) => {
            // 只取出一级目录
            var O_cate = {
              cateCode: cur.attribute.cateCode,
              cateName: cur.attribute.cateName,
              goods: []
            };
            acc.push(O_cate);
            return acc;
          }, []);
          return A_cateList;
        })

        .then(cateList => {
          var _cateList = [...cateList];
          // 为保证一定会有商品类别，顾采用同步请求
          getGoods(this.goodsQueryList).then(response => {
            // 备份一下，以便不时之需
            this.goodList = response.data.list;

            // 不需要返回值，但所有商品都要遍历到用forEach
            response.data.list.forEach(item => {
              // 循环找到对应的类别编码，如果找到了就插入到goods里面
              for (var i = 0; i < _cateList.length; i++) {
                if (_cateList[i].cateCode == item.firstCateCode) {
                  // 不用考虑去重
                  _cateList[i].goods.push(item);
                  // 跳出当本次循环，进行下次循环
                  continue;
                }
              }
            });
            this.goods = _cateList;
            // 在渲染之后计算每个商品的高度
            this.heigtCalculation();
          });
        });
    },

    //没有商品的分类不进行展示
    _otherinitData() {
      getGoods(this.goodsQueryList).then(response => {
        // 构造商品类别数组，并去重
        var A_cateList = [];
        response.data.list.forEach(item => {
          A_cateList.push(item.firstCateCode);
        });
        // 去重后，并转化为对象
        A_cateList = [...new Set(A_cateList)].map(code => {
          return {
            cateCode: code,
            cateName: "",
            goods: []
          };
        });
        // 将商品逐个归类到指定下
        this.goods = response.data.list.reduce((acc, cur) => {
          for (var i = 0; i < acc.length; i++) {
            // 如果已经有了就添加在goods数组里面
            if (acc[i].cateCode == cur.firstCateCode) {
              acc[i].cateName = cur.firstCateName;
              acc[i].goods.push(cur);
              continue;
            }
          }
          return acc;
        }, A_cateList);
        // 在渲染之后计算每个商品的高度
        this.heigtCalculation();
      });
    },

    // 启动滚动插件
    _initScroll() {
      this.$nextTick(() => {
        // 初始化实例
        // 菜单的滚动
        this.meunScroll = new BScroll(this.$refs.mw, {
          click: true
        });
        // 商品滚动
        this.goodScroll = new BScroll(this.$refs.gw, {
          click: true,
          // 实时监听滚动事件
          probeType: 3
        });
        // 监听滚动事件
        this.goodScroll.on("scroll", pos => {
          // 记录每次滚动的y值
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      });
    },

    //左侧类别点击
    handleSelect(index, $event) {
      //消除pc端的重复点击事件
      if (event._contsracte) {
        return;
      }

      //找到当前的dom
      var _el = this.$refs.goodList[index];

      //使用bs进行滚动
      this.goodScroll.scrollToElement(_el, 500);
    },

    // handleSelectGood() {},
    handleGoodDetail(good, event) {
      if (event._constracted) {
        return;
      }
      getGoodbySkuCode({ skuCode: good.skuCode }).then(response => {
        console.log(response.data);
        this.currentgood = response.data;
        this.GoodDetailVisible = true;
      });
    },
    //每个商品高度计算
    heigtCalculation() {
      this.$nextTick(() => {
        //获取当前good的高度
        var DOM_goodList = this.$refs.goodList;
        //第一个的高度是0
        var height = 0;
        this.listHeight.push(height);
        for (var i = 0; i < DOM_goodList.length; i++) {
          height += DOM_goodList[i].clientHeight;
          this.listHeight.push(height);
        }
        // console.log(this.listHeight);
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.goods {
  position: absolute;
  // border: rebeccapurple 1px solid;
  top: 26px;
  left: 0;
  bottom: 25px;
  overflow: hidden;
  display: flex;
  box-sizing: border-box;

  width: 100%;
  .meun-warpper {
    // width: 80px;
    // border: rebeccapurple 1px solid;
    flex: 0 0 80px;
    background-color: rgba(247, 242, 236, 0.8);

    .meun-item {
      text-align: center;
      height: 54px;
      width: 100px;
      line-height: 1rem;
      font-size: 1rem;
      padding: 0.08rem 0;
      display: table;
      font-size: 0;

      .text {
        display: table-cell;
        width: 100%;
        font-size: 1rem;
        vertical-align: middle;
        border-bottom: 1px solid rgba($color: #aaa, $alpha: 0.5);
      }
      // 当前选中
      &.current {
        position: relative;
        background-color: #ffffff;
        color: #c39862;
        z-index: 9;
        margin-top: -1px;
        font-weight: bold;
      }
    }
  }

  .goods-warpper {
    box-sizing: border-box;
    flex: 1;

    .title {
      padding-left: 1rem;
      height: 26px;
      line-height: 26px;
      font-size: 1rem;
      color: #aaa;
      font-weight: normal;
      border-left: #fff;
      background: rgba(252, 146, 24, 0.1);
    }
    .goods-item {
      display: flex;
      padding-bottom: #333 1px solid;
      padding-bottom: 8px;
      margin: 12px;
      border-bottom: #cea570 1px solid;
      // white-space: nowrap;

      &:last-child {
        border: none;
      }
    }
    .icon {
      flex-basis: 70px;
      img {
        width: 60px;
        height: 100px;
      }
    }
    .content {
      padding: 0, 1rem;
      position: relative;
      flex-grow: 1;
      height: 110px;
      margin: 5px;
      margin-right: 10px;
      width: 100%;
      // box-sizing: border-box;

      // display: flex;

      .name {
        margin: 8px 0;
        font-size: 1rem;
        font-size: 1rem;
        height: 1rem;
        line-height: 1rem;
        // white-space: nowrap;
        overflow: hidden;
      }

      .desc {
        max-height: 50px;
        overflow: hidden;
        //   overflow: visible;
        word-wrap: break-word;
        word-break: break-word;
        text-overflow: ellipsis;
        line-height: 1.2rem;
        font-size: 12px;
        opacity: 0.7;
        padding: 0.5rem 0.2rem;
        margin-right: 15px;
        min-width: 150px;
      }

      .price {
        margin-top: 5px;
        text-align: left;
        font-size: 16px;
        position: relative;

        .now {
          margin-right: 10px;
          font-size: 1rem;
          color: red;
        }
        .old {
          margin-right: 16px;
          font-size: 0.8rem;
          color: #ccc;
          text-decoration: line-through;
        }
        .saleNum {
          font-size: 0.8rem;

          color: #cea570;
          float: right;
        }
      }
    }
  }
}
</style>
