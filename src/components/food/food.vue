<template lang="html">
    <transition name="move">
      <div class="food" v-show="showFlag" ref="foods">
        <div class="food-content">
          <div class="img-header">
            <img :src="food.image">
            <div class="back" @click="hide">
              <i class="icon-arrow_lift"></i>
            </div>
          </div>
          <div class="content">
            <h1 class="title">{{food.name}}</h1>
            <div class="detail">
              <span class="sell-count">月售{{food.sellCount}}份</span>
              <span class="rating">好评率{{food.rating}}%</span>
            </div>
            <div class="price">
              <span class="new-price">￥{{food.price}}</span>
              <span v-show="food.oldPrice" class="old-price">￥{{food.oldPrice}}</span>
            </div>
            <div class="cartControl-wrap">
              <cartControl @add="addFood" :food="food"></cartControl>
            </div>
            <transition name="fade">
              <div class="buy" @click="addFirst" v-show="!food.count || food.count === 0">加入购物车</div>
            </transition>
          </div>
          <split v-show="food.info"></split>
          <div class="food-desc" v-show="food.info">
            <h1 class="title">商品介绍</h1>
            <p class="text">{{food.info}}</p>
          </div>
          <split></split>
          <div class="rating">
            <div class="title">商品评价</div>
            <ratingSelect :selectType="selectType" :onlyContent="onlyContent" :desc="desc"
            :ratings="food.ratings" @select="selectRating" @toggle="toggleContent"></ratingSelect>
            <div class="rating-wrap">
              <ul v-show="food.ratings && food.ratings.length">
                <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item">
                  <div class="user">
                    <div class="name">{{rating.username}}</div>
                    <img class="avatar" width="12" height="12" :src="rating.avatar">
                  </div>
                  <div class="time">{{rating.rateTime | formatDate}}</div>
                  <p class="text">
                    <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
                    {{rating.text}}
                  </p>
                </li>
              </ul>
              <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
            </div>
          </div>
        </div>

      </div>
    </transition>

</template>
<script>
    import Bscroll from 'better-scroll'
    import Vue from 'vue'
    import {formatDate} from '../../common/js/date'
    import cartControl from '../cartControl/cartControl'
    import split from '../split/split'
    import ratingSelect from '../ratingSelect/ratingSelect'

    const ALL = 2

    export default{
        props:{
            food:Object
        },
        data(){
            return{
              showFlag:false,
              selectType:ALL,
              onlyContent:true,
              desc:{
                  all:'全部',
                  positive:'推荐',
                  negative:'吐槽'
              }
          }
        },
        methods:{
            show(){
                this.showFlag = true;
                this.selectType = ALL;
                this.onlyContent = true;
                this.$nextTick(() => {
                if (!this.scroll) {
                  this.scroll = new Bscroll(this.$refs.foods, {
                    click: true
                  })
                } else {
                  this.scroll.refresh();
                }
              });
            },
            hide(){
                this.showFlag =  false;
            },
            addFood(target) {
              this.$emit('add', target);
            },
            addFirst(event){
                if(!event._constructed){
                    return;
                }
                this.$emit('add',event.target)
                Vue.set(this.food,'count',1)
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
            },
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
            }
        },
        filters:{
          formatDate(time){
              let date = new Date(time);
              return formatDate(date,'yyy-MM-dd hh:mm');
          }
        },
        components:{
            cartControl,
            split,
            ratingSelect
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/icon"
  @import "../../common/stylus/mixin"
  .food
    position :fixed
    left :0
    top:0
    bottom :48px
    z-index :30
    width :100%
    background :#fff
    transform :translate3d(0,0,0)
    &.move-enter-active,&.move-leave-active
      transition :all 0.2s linear
    &.move-enter,&.move-leave-active
      transform :translate3d(100%,0,0)
    .food-content
      .img-header
        position:relative
        width :100%
        height :0
        padding-top :100%
        img
          position :absolute
          top :0
          left :0
          width :100%
          height :100%
        .back
          position :absolute
          top :10px
          left :0px
          .icon-arrow_lift
            display :block
            padding :10px
            font-size :20px
            color :#fff
      .content
        padding :18px
        position :relative
        .title
          line-height :14px
          font-size :14px
          margin-bottom :8px
          color :rgb(7,17,27)
          font-weight :700
        .detail
          margin-bottom :18px
          line-height :10px
          font-size :0
          .sell-count,.rating
            font-size :10px
            color :rgb(147,153,159)
          .sell-count
            margin-right :12px
        .price
          line-height :24px
          .new-price
            font-size :14px
            color :rgb(240,20,20)
            font-weight :700
          .old-price
            text-decoration :line-through
            font-size :10px
            color :rgb(143,157,159)
            font-weight :700
        .cartControl-wrap
          position :absolute
          right :12px
          bottom :12px
        .buy
          position :absolute
          right :18px
          bottom :18px
          z-index: 10
          height :24px
          line-height :24px
          padding :0 12px
          box-sizing :border-box
          font-size :10px
          border-radius :12px
          color :#fff
          background :rgb(0,160,220)
          opacity: 1
          &.fade-enter-active, &.fade-leave-active
            transition: all 0.2s
          &.fade-enter, &.fade-leave-active
            opacity: 0
            z-index: -1
      .food-desc
        padding :18px
        .title
          line-height :14px
          margin-bottom :6px
          font-size :14px
          color :rgb(7,17,27)
        .text
          line-height :16px
          font-size :12px
          font-weight :200
          color :rgb(77,85,93)
          padding :0 6px
      .rating
        padding-top :18px
        .title
          line-height :14px
          font-size :14px
          margin-left :18px
          color :rgb(7,17,27)
        .rating-wrap
          padding :0px 18px
          .rating-item
            position :relative
            padding :16px 0
            border-1px(rgba(7,17,27,0.1))
            .user
              position :absolute
              right :0
              top :16px
              line-height :12px
              font-size :0
              .name
                display :inline-block
                vertical-align :top
                font-size :10px
                color :rgb(147,153,159)
                margin-right :6px
              .avatar
                border-radius :50%
            .time
              margin-bottom :6px
              line-height :12px
              font-size :10px
              color :rgb(147,153,159)
            .text
              line-height :16px
              font-size :12px
              color :rgb(7,17,27)
              .icon-thumb_up,.icon-thumb_down
                margin-right :4px
                line-height :16px
                font-size :12px
              .icon-thumb_up
                color :rgb(0,160,220)
              .icon-thumb_down
                color :rgb(147,153,159)
          .no-rating
            padding :16px 0
            font-size :12px
            color :rgb(147,153,159)
</style>
