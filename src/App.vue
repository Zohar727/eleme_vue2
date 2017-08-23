<template>
  <div>
    <v-header :seller="seller">

    </v-header>
    <div class="tab border-1px">
        <div class="tab-item">
          <router-link to='/goods'>商品</router-link>
          </div>
        <div class="tab-item">
          <router-link to='/ratings'>评价</router-link>
          </div>
        <div class="tab-item">
          <router-link to='/seller'>商家</router-link>
          </div>
      </div>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>

  </div>
</template>

<script>
  import {urlParse} from './common/js/util'
  import header from './components/header/header.vue'
  const ERR_OK = 0;
  export default {
    data() {
      return {
        seller: {
          id: (() => {
            let queryParam = urlParse();
            return queryParam.id;
          })()
        }
      };
    },
    created() {

      //修改get文件路径
      this.$http.get(`./static/seller.json`).then((response) => {
          //response = response.body;
          this.seller = Object.assign({}, this.seller, response.data);
      }).catch((error) => {
          console.log(error);
      })
    },
    components: {
      'v-header': header
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "common/stylus/mixin.styl"
  @import "common/stylus/base.styl"
  .tab{

      display:flex
      width:100%
      height:40px
      line-height:40px
      border-1px(rgba(7,17,27,0.1))
  }
  .tab .tab-item{
    flex:1
    text-align:center
  }
  .tab .tab-item a{
        display:block
        font-size :14px
        color :rgb(77,85,93)
    }
  .tab .tab-item a.active{
    color :rgb(240,20,20)
  }



</style>
