<template lang="html">
    <div class="header">
      <div class="content-wrap">
        <div class="avatar">
          <img height="64" width="64" :src="seller.avatar">
        </div>
        <div class="content">
          <div class="title">
            <span class="brand"></span>
            <span class="name">{{seller.name}}</span>
          </div>
          <div class="description">
            {{seller.description}} / {{seller.deliveryTime}}分钟送达
          </div>
          <div v-if="seller.supports" class="support" >
            <span class="icon" :class="classMap[seller.supports[0].type]"></span>
            <span class="text">{{seller.supports[0].description}}</span>
          </div>
        </div>
        <div class="support-count" v-if="seller.supports" @click="showDetail">
          <span class="count">{{seller.supports.length}}个</span>
          <i class="icon-arrow_right"></i>
        </div>
      </div>
      <div class="notice-wrap" @click="showDetail">
        <span class="notice-title" ></span><span class="notice-text">{{seller.bulletin}}</span>
        <i class="icon-arrow_right"></i>
      </div>
      <div class="background">
        <img :src="seller.avatar" width="100%" height="100%">
      </div>
      <transition name="fade">
      <div class="detail" v-show="detailShow" transition="fade">
        <div class="detail-wrap clearfix">
          <div class="detail-main">
            <h1 class="detail-name">{{seller.name}}</h1>
            <div class="star-wrap">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="detail-title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class="support-item" v-for="(item,index) in seller.supports">
                <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                <span class="text">{{seller.supports[index].description}}</span>
              </li>
            </ul>
            <div class="detail-title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="detail-notice">
              <p>{{seller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="hideDetail">
          <i class="icon-close"></i>
        </div>
      </div>
      </transition>
    </div>
</template>
<script type="text/ecmascript-6">
    import star from "../star/star"
    export default{
      props:{
          seller:{
              type:Object
          }
      },
      data(){
          return{
            detailShow:false
          };

      },
      methods:{
          showDetail() {
              this.detailShow = true;
          },
          hideDetail(){
              this.detailShow = false;
          }
      },
      created(){
          this.classMap = ['decrease','discount','special','invoice','guarantee']
      },
      components:{
          star
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus" scoped>
  @import "../../common/stylus/mixin"
  @import "../../common/stylus/icon"
  @import "../../common/stylus/base"
  .header{
    color :#fff;
    background-color :rgba(7,17,27,0.5);
    position :relative;
    overflow :hidden;
  }
  .header .content-wrap{
    position :relative;
    padding :24px 12px 18px 24px;
    font-size :0;
  }
  .content-wrap .avatar{
    display :inline-block;
    vertical-align top;
  }
  .avatar img{
    border-radius :2px;
  }
  .content-wrap .content{
    display :inline-block;
    margin-left :16px;
    font-size :14px;
  }
  .content .title{
    margin :0px 0px 8px 0px;
  }
  .title .brand{
    vertical-align :top;
    display :inline-block;
    width :30px;
    height :18px;
    bg-img('brand');
    background-size :30px 18px;
    background-repeat :no-repeat;
  }
  .title .name{
    margin-left :6px;
    font-size :16px;
    line-height :18px;
    font-weight :bold;
  }
  .content .description{
    font-size :12px;
    line-height :12px;
    margin-bottom :10px;
  }
  .support .icon{
    display :inline-block;
    width :12px;
    height :12px;
    margin-right :2px;
    background-size :12px 12px;
    background-repeat :no-repeat;
  }
  .support .decrease{
    bg-img('decrease_1');
  }
  .support .discount{
    bg-img('discount_1');
  }
  .support .guarantee{
    bg-img('guarantee_1');
  }
  .support .invoice{
    bg-img('invoice_1');
  }
  .support .special{
    bg-img('special_1');
  }
  .support .text{
    vertical-align :top;
    line-height :13px;
    font-size :10px;
  }
  .support-count{
    position :absolute;
    right :12px;
    bottom :18px;
    padding :0 8px;
    height :24px;
    line-height :24px;
    border-radius :14px;
    background-color :rgba(0,0,0,0.2);
    text-align :center;
  }
  .support-count .count{
    font-size :10px;
    vertical-align :top;
  }
  .support-count .icon-arrow_right{
    margin-left :2px;
    line-height :24px;
    font-size :10px;
  }
  .notice-wrap{
    position :relative;
    height :28px;
    line-height :28px;
    padding : 0 22px 0 12px;
    white-space :nowrap;
    overflow :hidden;
    text-overflow :ellipsis;
    background-color :rgba(7,17,27,0.2);
  }
  .notice-wrap .notice-title{
    display :inline-block;
    vertical-align :top;
    margin-top :7px;
    width :22px;
    height :12px;
    bg-img('bulletin');
    background-size :22px 12px;
    background-repeat :no-repeat;
  }
  .notice-wrap .notice-text{
    vertical-align :top;
    font-size :10px;
    margin :0 4px;
  }
  .notice-wrap .icon-arrow_right{
    font-size :10px;
    margin-left :4px;
    position :absolute;
    bottom :7px;
    right :12px;
  }
  .background{
    position :absolute;
    top :0;
    left :0;
    width :100%;
    height :100%;
    z-index :-1;
    filter :blur(10px);
  }
  .detail{
    position :fixed;
    top :0;
    left :0;
    z-index :100;
    width :100%;
    height :100%
    overflow :auto;
    background-color :rgba(7,17,27,0.8);
    backdrop-filter :blur(10px);
    -webkit-backdrop-filter :blur(10px);
  }
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s
  }
  .fade-enter, .fade-leave-active {
    opacity: 0;
  }
  .detail-wrap{
    min-height :100%;
    width :100%;
  }
  .detail-wrap .detail-main{
    margin-top :64px;
    padding-bottom :64px;
  }
  .detail-close{
    position :relative;
    width :32px;
    height :32px;
    margin :-64px auto 0 auto;
    clear :both;
    font-size :32px;
  }
  .detail-main .detail-name{
    font-size :16px;
    line-height :16px;
    font-weight :700;
    width :100%;
    text-align :center;
  }
  .star-wrap{
    margin-top :18px;
    padding :2px 0;
    text-align :center;
  }
  .detail-title{
    display :flex;
    width :80%;
    margin :28px auto 24px auto;
  }
  .detail-title .line{
    flex :1;
    position :relative;
    top :-6px;
    border-bottom :1px solid rgba(255,255,255,0.2);
  }
  .detail-title .text{
    padding :0px 12px;
    font-size :14px;
    font-weight :700;
  }
  .detail-main .supports{
    width :80%;
    margin :0 auto;
  }
  .supports .support-item{
    padding :0 12px;
    margin-bottom :12px;
    font-size :0;
  }
  .supports .support-item:last-child{
    margin-bottom :0;
  }
  .support-item .icon{
    display :inline-block;
    width :16px;
    height :16px;
    vertical-align :top;
    margin-right :6px;
    background-size :16px 16px;
    background-repeat :no-repeat;
  }
  .support-item .decrease{
    bg-img('decrease_2');
  }
  .support-item .discount{
    bg-img('discount_2');
  }
  .support-item .guarantee{
    bg-img('guarantee_2');
  }
  .support-item .invoice{
    bg-img('invoice_2');
  }
  .support-item .special{
    bg-img('special_2');
  }
  .support-item .text{
    line-height :16px;
    font-size :12px;
  }
  .detail-notice{
    width :80%;
    margin :0 auto;
  }
  .detail-notice p{
    font-size :12px;
    line-height :24px;
  }
</style>
