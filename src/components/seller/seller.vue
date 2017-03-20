<template lang="html">
    <div class="seller" ref="seller">
      <div class="seller-content">
        <div class="overView">
          <h1 class="title">{{seller.name}}</h1>
          <div class="desc">
            <star :size="36" :score="seller.score"></star>
            <span class="text">({{seller.ratingCount}})</span>
            <span class="text">月售{{seller.sellCount}}单</span>
          </div>
          <ul class="remark">
            <li class="block">
              <h2>起送价</h2>
              <div class="text">
                <span class="bigNum">{{seller.minPrice}}</span>元
              </div>
            </li>
            <li class="block">
              <h2>商家配送</h2>
              <div class="text">
                <span class="bigNum">{{seller.deliveryPrice}}</span>元
              </div>
            </li>
            <li class="block">
              <h2>平均配送时间</h2>
              <div class="text">
                <span class="bigNum">{{seller.deliveryTime}}</span>分钟
              </div>
            </li>
          </ul>
          <div class="favorite" @click="toggleFavorite">
            <span class="icon-favorite" :class="{'active':favorite}"></span>
            <div class="text">{{favoriteText}}</div>
          </div>
        </div>
        <split></split>
        <div class="notice">
          <h1>公告与活动</h1>
          <div class="text-wrap">
            <p class="text">{{seller.bulletin}}</p>
          </div>
          <ul v-if="seller.supports" class="supports">
            <li class="support-item" v-for="(item,index) in seller.supports">
              <span class="icon" :class="classMap[seller.supports[index].type]"></span>
              <span class="text">{{seller.supports[index].description}}</span>
            </li>
          </ul>
        </div>
        <split></split>
        <div class="pics">
          <h1 class="title">商家实景</h1>
          <div class="pic-wrap" ref="picWrapper">
            <ul class="pic-list" ref="picList">
              <li class="pic-item" v-for="pic in seller.pics">
                <img :src="pic" width="120" height="90">
              </li>
            </ul>
          </div>
        </div>
        <split></split>
        <div class="infos">
          <h1 class="title">商家信息</h1>
          <ul>
            <li class="infos-item" v-for="info in seller.infos">{{info}}</li>
          </ul>
        </div>
      </div>

    </div>
</template>
<script>
    import star from '../star/star'
    import split from '../split/split'
    import Bscroll from  'better-scroll'
    import {saveToLocal, loadFromLocal} from '../../common/js/store'
    export default{
        props:{
            seller:{
                type:Object
            }
        },
        data(){
            return{
                //favorite:false
                favorite:(() => {
                    return loadFromLocal(this.seller.id,'favorite',false)
                    //console.log(loadFromLocal(this.seller.id,'favorite',false));
                })()
            }
        },
        computed:{
          favoriteText(){
              return this.favorite ? '已收藏':'收藏';
          }
        },
        created(){
        this.classMap = ['decrease','discount','special','invoice','guarantee'];
      },
      watch:{
        'seller'() {
          this.$nextTick(() => {
            this._initScroll();
            this._initPics();
          });
        }
      },
      mounted() {
        this.$nextTick(() => {
          this._initScroll();
          this._initPics();
        });
      },
      methods:{
            _initScroll() {
              if (!this.scroll) {
                this.scroll = new Bscroll(this.$refs.seller, {
                  click: true
                });
              } else {
                this.scroll.refresh();
              }
            },
            _initPics() {
                let picWidth = 120;
                let margin = 6;
                let width = (picWidth+margin)*this.seller.pics.length-margin;
                this.$refs.picList.style.width = width + 'px';
                this.$nextTick(() => {
                    if(!this.picScroll){
                        this.picScroll = new Bscroll(this.$refs.picWrapper,{
                            scrollX:true,
                            eventPassthrough: 'vertical'
                        })
                    }else {
                        this.picScroll.refresh();
                    }
                })
            },
            toggleFavorite(event){
          if(!event._constructed){
            return;
          }else {
            this.favorite=!this.favorite
            saveToLocal(this.seller.id,'favorite',this.favorite)
          }
        }
      },
        components:{
            star,
            split
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  .seller
    position :absolute
    top :174px
    bottom :0
    left :0
    width :100%
    overflow :hidden
    .overView
      padding :18px
      position :relative
      .title
        margin-bottom :8px
        line-height :14px
        font-size :14px
        color :rgb(7,17,27)
      .desc
        padding-bottom :18px
        border-1px(rgba(7,17,27,0.1))
        font-size :0
        .star
          display :inline-block
          margin-right :8px
          vertical-align :top
        .text
          display :inline-block
          line-height :18px
          font-size :10px
          color :rgb(77,85,93)
          margin-right :12px
      .remark
        display :flex
        padding-top :18px
        .block
          flex :1
          text-align :center
          border-right :1px solid rgba(7,17,27,0.1)
          &:last-child
            border :none
          h2
            margin-bottom 4px
            line-height :20px
            font-size :10px
            color :rgb(77,85,93)
          .text
            font-size :10px
            .bigNum
              line-height :24px
              font-weight :200px
              font-size :24px
              color :rgb(7,17,27)
      .favorite
        position :absolute
        width: 50px
        right :11px
        top :18px
        text-align :center
        .icon-favorite
          display :block
          margin-bottom :4px
          line-height :24px
          font-size :24px
          color :#d4d6d9
          &.active
            color :rgb(240,20,20)
        .text
          line-height :10px
          font-size :10px
          color :rgb(77,85,93)
    .notice
      padding :18px 18px 0px 18px
      h1
        margin-bottom :8px
        line-height :14px
        font-size :14px
        color :rgb(7,17,27)
      .text-wrap
        padding :0 12px 16px 12px
        border-1px(rgba(7,17,27,0.1))
        .text
          line-height :24px
          font-size :12px
          font-weight :200
          color :rgb(240,20,20)
      .supports
        .support-item
          padding: 16px 12px
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 0
          &:last-child
            border-none()
          .icon
            display: inline-block
            width: 16px
            height: 16px
            vertical-align: top
            margin-right: 6px
            background-size: 16px 16px
            background-repeat: no-repeat
            &.decrease
              bg-img('decrease_4')
            &.discount
              bg-img('discount_4')
            &.guarantee
              bg-img('guarantee_4')
            &.invoice
              bg-img('invoice_4')
            &.special
              bg-img('special_4')
          .text
            line-height: 16px
            font-size: 12px
            color: rgb(7, 17, 27)
    .pics
      padding :18px
      .title
        margin-bottom :12px
        line-height :14px
        font-size :14px
        color :rgb(7,17,27)
      .pic-wrap
        width :100%
        overflow :hidden
        white-space :nowrap
        .pic-list
          font-size :0
          .pic-item
            display :inline-block
            margin-right :6px
            width :120px
            height :90px
            &:last-child
              margin-right :0
    .infos
      padding :18px
      .title
        padding-bottom :12px
        line-height :14px
        font-size :14px
        color :rgb(7,17,27)
        border-1px(rgba(7,17,27,0.1))
      .infos-item
        padding :16px 12px
        line-height :16px
        font-size :12px
        font-weight :200
        color :rgb(7,17,27)
        border-bottom :1px solid rgba(7,17,27,0.1)
        &:last-child
          border :none
</style>
