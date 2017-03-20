<template lang="html">
    <div class="ratings" ref="ratings">
        <div class="ratings-content">
          <div class="overView">
            <div class="overView-left">
              <div class="score">{{seller.score}}</div>
              <div class="title">综合评分</div>
              <div class="rankRate">高于周边商家{{seller.rankRate}}%</div>
            </div>
            <div class="overView-right">
              <div class="score-wrap">
                <span class="title">服务态度</span>
                <star :size="36" :score="seller.serviceScore"></star>
                <span class="score">{{seller.serviceScore}}</span>
              </div>
              <div class="score-wrap">
                <span class="title">商品评分</span>
                <star :size="36" :score="seller.foodScore"></star>
                <span class="score">{{seller.foodScore}}</span>
              </div>
              <div class="delivery">
                <span class="title">送达时间</span>
                <span class="score">{{seller.deliveryTime}}分钟</span>
              </div>
            </div>
          </div>
          <split></split>
          <ratingSelect :selectType="selectType" :onlyContent="onlyContent"
                        :ratings="ratings" @select="selectRating" @toggle="toggleContent"></ratingSelect>
          <div class="rating-wrap">
            <ul>
              <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType,rating.text)">
                <div class="avatar">
                  <img :src="rating.avatar" width="28" height="28">
                </div>
                <div class="content">
                  <h1 class="name">{{rating.username}}</h1>
                  <div class="star-wrap">
                    <star :size="24" :score="rating.score"></star>
                    <span class="delivery" v-show="rating.deliveryTime" >{{rating.deliveryTime}}分钟送达</span>
                  </div>
                  <div class="text">{{rating.text}}</div>
                  <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                    <span class="icon-thumb_up"></span>
                    <span class="item" v-for="item in rating.recommend">{{item}}</span>
                  </div>
                  <div class="time">{{rating.rateTime | formatDate}}</div>
                </div>
              </li>
            </ul>
          </div>
        </div>
    </div>
</template>
<script>
  import star from '../star/star'
  import ratingSelect from '../ratingSelect/ratingSelect'
  import split from '../split/split'
  import {formatDate} from '../../common/js/date'
  import Bscroll from 'better-scroll'
  const ALL = 2
  const ERR_OK=0
    export default{
        props:{
            seller:{
                type:Object
            }
        },
        data() {
            return{
              ratings:[],
              showFlag:false,
              selectType:ALL,
              onlyContent:true
            };

        },
        methods:{
          selectRating(type){
            this.selectType = type;
            //console.log('nowType'+this.selectType);
            this.$nextTick(() => {
              this.scroll.refresh();
            });
          },
          toggleContent(){
            this.onlyContent = !this.onlyContent;
            this.$nextTick(() => {
              this.scroll.refresh();
            });
          },
          needShow(type,text){
            if(this.onlyContent && !text){
              return false;
            }
            if(this.selectType === ALL){
              return true;
            }else {
              return type === this.selectType
            }
          }
        },
        created(){
            this.$http.get('/static/ratings.json').then(response => {
              //response = response.body;
              //if(response.errno === ERR_OK){
                  this.ratings = response.data;
                  this.$nextTick(() => {
                      this.scroll = new Bscroll(this.$refs.ratings,{
                          click:true
                      })
                  })

            })
        },
        filters:{
        formatDate(time){
          let date = new Date(time);
          return formatDate(date,'yyy-MM-dd hh:mm');
        }
      },
        components:{
            star,
            ratingSelect,
            split
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/icon"
  @import "../../common/stylus/mixin"
  .ratings
    position:absolute
    top :174px
    bottom :0
    left :0
    width :100%
    overflow :hidden
    .overView
      display :flex
      padding :18px 0
      .overView-left
        flex :0 0 137px
        padding :6px 0
        width :137px
        border-right :1px solid rgba(7,17,27,0.1)
        text-align :center
        @media only screen and (max-width :320px)
          flex:0 0 120px
          width :120px
        .score
          margin-bottom :6px
          line-height :28px
          font-size :24px
          color :rgb(255,153,0)
        .title
          margin-bottom :8px
          line-height :12px
          font-size :12px
          color :rgb(7,17,27)
        .rankRate
          margin-bottom :6px
          line-height :10px
          font-size :10px
          color :rgb(147,153,159)
      .overView-right
        flex :1
        padding :6px 0px 24px 24px
        @media only screen and (max-width :320px)
          padding-left :6px
        .score-wrap
          margin-bottom :8px
          font-size :0
          .title
            display :inline-block
            line-height :18px
            font-size :12px
            color :rgb(7,17,27)
          .star
            display :inline-block
            margin :0 12px
            vertical-align :top
          .score
            display :inline-block
            line-height :18px
            font-size :12px
            color :rgb(255,153,0)
        .delivery
          .title
            margin-bottom :8px
            line-height :12px
            font-size :12px
            color :rgb(7,17,27)
          .score
            line-height :18px
            margin :0 12px
            font-size :12px
            color :rgb(147,153,159)
    .rating-wrap
      padding :0 18px
      .rating-item
        padding :18px 0
        display :flex
        border-1px(rgba(7,17,27,0.1))
        .avatar
          flex :0 0 28px
          img
            border-radius :50%
        .content
          flex :1
          margin-left :12px
          position :relative
          .name
            line-height :12px
            font-size :10px
            color :rgb(7,17,27)
            margin-bottom :4px
          .star-wrap
            margin-bottom :6px
            .star
              display :inline-block
            .delivery
              display :inline-block
              line-height :12px
              font-size :10px
              font-weight :100
              color :rgb(147,153,159)
          .text
            line-height :18px
            font-size :12px
            color :rgb(7,17,27)
            margin-bottom :8px
          .recommend
            font-size :0
            line-height :16px
            .icon-thumb_up
              font-size :12px
              color :rgb(0,160,220)
              margin-right :8px
              vertical-align :top
            .item
              padding :0 6px
              height :16px
              color :rgb(147,153,159)
              font-size :9px
              border :1px solid rgba(7,17,27,0.1)
              border-radius :1px
              margin-right :8px
          .time
            position :absolute
            top :0
            right :0
            line-height :12px
            font-size :10px
            font-weight :200
            color :rgb(147,153,159)
</style>
