<template>
  <div class="goods p-r bg">

    <Head @search="getData" :showSearch="true"></Head>
    <div class="search-row">
      <div class="logo mt20 mb20 f-r f-a-c f-j-s">
        <div class="font-default font30"> </div>
      </div>
    </div>

    <div class="goods-content center">
      <el-breadcrumb separator-class="el-icon-arrow-right">
        <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
        <el-breadcrumb-item>商品详情</el-breadcrumb-item>
        <el-breadcrumb-item>{{info.goodsType}}</el-breadcrumb-item>
        <el-breadcrumb-item>{{info.goodsName}}</el-breadcrumb-item>
      </el-breadcrumb>

      <div class="row1 mt20 f-r f-a-l ">
        <img :src="info.goodsImg" style="width:460px;height:500px;border: 1px #c7c7c7 solid;object-fit: cover">
        <div class="f-c ml30 w100 p-r" style="height:500px">
          <div class="font-w600 font22">{{info.goodsName}}</div>
          <p>{{info.tip}}</p>
          <div class="price font14 p10 w100">
            售价
            <span class="font30 ml40">¥{{info.goodsValue}}</span>
          </div>
          <div class="type font14 p10 w100 font-grey">
            数量
            <span class="ml40 font-black">{{info.goodsNum}}</span>
          </div>
          <div class="p-a btn">
            <el-button type="danger" round class="car" @click="addcar(info)">加入购物车</el-button>
          </div>
        </div>
      </div>

      <div class="row2 mt20">
        <el-tabs v-model="activeName">
          <el-tab-pane label="商品详情" name="details" v-html="info.goodsDes" class="details-content"></el-tab-pane>
          <el-tab-pane label="评论详情" name="comment">
            <comment-section v-if="activeName === 'comment'" :itemId="id" :list="list" @update-comments="updateComments" />
          </el-tab-pane>
        </el-tabs>
      </div>
    </div>

    <Footer></Footer>
  </div>
</template>

<script>
import Head from "@/components/Head";
import Footer from "@/components/Footer";
import * as api from "@/api/api";
import { mapGetters, mapMutations } from "vuex";
import CommentSection from "@/views/my/components/CommentSection";

export default {
  components: { Head, Footer, CommentSection },
  data() {
    return {
      id: "",
      activeName: "details",
      rate: 5,
      info: {},
      list:[]
    };
  },
  created() {
    this.id = this.$route.query.id;
    this.getData();
  },
  computed: {
    ...mapGetters(["userinfo"]),
  },
  mounted() {},
  methods: {
    ...mapMutations(["add"]),
    //获取商品和评论
    getData() {
      api.GoodsById(this.id).then((res) => {
        this.info = res;
      });
      api.commentByGoodsId({
        id: this.id,
        pageNo: 1,
        size: 9999
      }).then((res) => {
        this.list = res.records;
      });
    },

    // 更新评论列表
    updateComments(newComments) {
      this.list = newComments;
    },
    //加入购物车
    addcar(item) {
      if (Object.keys(this.userinfo).length == 0) {
        this.$message.error("请先登录!");
        return;
      } else {
        this.add(item);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.goods {
  background-color: #fff;
  .search-row {
    width: 1200px;
    left: 50%;
    margin-left: -600px;
    position: relative;
    .logo {
      .search {
        input {
          border-radius: 0px;
        }
        ::v-deep .el-input__inner {
          border-radius: 0px;
        }
        ::v-deep .el-input-group__append {
          background-color: #fff;
          border-radius: 0px;
          button {
            border-radius: 0px;
          }
        }
      }
    }
  }
  .goods-content {
    min-height: 70vh;
    ::v-deep .el-tabs__item {
      font-size: 16px;
    }
    ::v-deep .el-rate__icon {
      font-size: 22px;
    }
    .row1 {
      .price {
        color: #fff;
        background-color: #c41a21;
        background: linear-gradient(90deg, #f23038 0%, #c41a21 100%);
        border-radius: 8px 8px 0 0;
      }
      .type {
        background-color: #f7f9fa;
      }
      .btn {
        bottom: 0;
        width: 300px;
        left: 50%;
        margin-left: -150px;
        button {
          border-radius: 30px;
        }
        .car {
          background-color: #c41a21;
          background: linear-gradient(90deg, #f23038 0%, #c41a21 100%);
          height: 50px;
          width: 200px;
        }
      }
    }
  }
}
.details-content {
  ::v-deep img {
    width: 100%;
  }
  min-height: 200px;
  margin-bottom: auto;
}
.center {
  width: 1200px;
  position: relative;
  left: 50%;
  margin-left: -600px;
}
</style>
