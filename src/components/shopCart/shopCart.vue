<template lang="html">
  <div>
    <div class="shopCart">
      <div class="content" @click="toggleList">
        <div class="content-left">
            <div class="logo-wrap">
              <div class="logo" :class="{'highlight':totalCount>0}">
                <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
              </div>
              <div class="num" v-show="totalCount">{{totalCount}}</div>
            </div>
            <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
            <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
        </div>
        <div class="content-right" @click.stop.prevent="pay">
          <div class="pay" :class="payClass">
            {{payDesc}}
          </div>
        </div>
      </div>
      <div class="ball-container">
        <div v-for="ball in balls">
          <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
            <div class="ball" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <transition name="fold">
      <div class="shopCart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-item" ref="listContent">
          <ul>
            <li class="food" v-for="food in selectFoods">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price * food.count}}</span>
              </div>
              <div class="cartControlWarp">
                <cartControl @add="addFood" :food="food"></cartControl>
              </div>
            </li>
          </ul>
        </div>
      </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" @click="hideList" v-show="listShow"></div>
    </transition>
  </div>
</template>
<script>
  import Bscroll from 'better-scroll'
  import cartControl from '../cartControl/cartControl'
    export default{
      props:{
        selectFoods:{
          type:Array,
          default(){
              return [{
                  price:10,
                  count:1
              }];
          }
        },
        deliveryPrice:{
          type:Number,
          default:0
        },
        minPrice:{
          type:Number,
          default:0
        }
      },
      data(){
        return{
            balls:[
              {
                show:false
              },
              {
                show:false
              },
              {
                show:false
              },
              {
                show:false
              },
              {
                show:false
              },
            ],
            dropBall:[],
            fold:true
        }
      },
      computed:{
          totalPrice(){
              let total = 0;
              this.selectFoods.forEach((food) => {
                  total += food.price * food.count
              });
              return total;
          },
          totalCount(){
              let count = 0;
              this.selectFoods.forEach((food) => {
                  count += food.count
              });
              return count;
          },
          payDesc(){
              if(this.totalPrice === 0){
                  return `￥${this.minPrice}元起送`;
              }else if (this.totalPrice<this.minPrice){
                  let differ = this.minPrice - this.totalPrice;
                  return `还差￥${differ}元起送`
              }else {
                  return '去结算';
              }
          },
          payClass(){
              if(this.totalPrice<this.minPrice){
                  return 'unenough'
              }else {
                  return 'enough'
              }
          },
          listShow(){
              if(!this.totalCount){
                  this.fold = true;
                  return false;
              }
              let show = !this.fold;
              if(show){
                  this.$nextTick(() => {
                      if(!this.scroll){
                        this.scroll = new Bscroll(this.$refs.listContent,{
                          click:true
                        });
                      }else {
                          this.scroll.refresh();
                      }


                  });
              }
              return show;
          }
      },
      methods:{
          drop(el){
            //console.log(el);
            for(let i=0 ; i< this.balls.length ;i++){
                let ball = this.balls[i];
                if(!ball.show){
                    ball.show = true;
                    ball.el =el;
                    this.dropBall.push(ball);
                    return;
                }
            }
          },
        addFood(target) {
          this.drop(target);
        },
        beforeDrop(el) {
          let count = this.balls.length;
          while (count--) {
            let ball = this.balls[count];
            if (ball.show) {
              let rect = ball.el.getBoundingClientRect();
              let x = rect.left - 32;
              let y = -(window.innerHeight - rect.top - 22);
              el.style.display = '';
              el.style.webkitTransform = `translate3d(0,${y}px,0)`;
              el.style.transform = `translate3d(0,${y}px,0)`;
              let inner = el.getElementsByClassName('inner-hook')[0];
              inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
              inner.style.transform = `translate3d(${x}px,0,0)`;
            }
          }
        },
        dropping(el, done) {
          let rf = el.offsetHeight;
          this.$nextTick(() => {
            el.style.webkitTransform = 'translate3d(0,0,0)';
            el.style.transform = 'translate3d(0,0,0)';
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = 'translate3d(0,0,0)';
            inner.style.transform = 'translate3d(0,0,0)';
            el.addEventListener('transitionend', done);
          });
        },
        afterDrop(el) {
          let ball = this.dropBall.shift();
          if (ball) {
            ball.show = false;
            el.style.display = 'none';
          }
        },
        toggleList(){
              if(!this.totalCount){
                  //console.log(this.totalCount);
                  return;
              }
              this.fold = !this.fold;
        },
        empty(){
            this.selectFoods.forEach((food) => {
                food.count= 0;
            })
        },
        hideList(){
            this.fold = true;
        },
        pay(){
            if(this.totalPrice < this.minPrice){
                return;
            }
            window.alert(`去支付${this.totalPrice}元`);
        }
      },
      components:{
          cartControl
      }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/icon"
  @import "../../common/stylus/mixin"
  .shopCart
    position :fixed
    left :0
    bottom :0
    z-index :50
    width :100%
    height :48px
    background-color :aquamarine
    .content
      display :flex
      background :#141d27
      font-size :0
      .content-left
        flex :1
        .logo-wrap
          display: inline-block
          position :relative
          top :-10px;
          margin :0 16px
          padding :6px
          width :56px
          height :56px
          box-sizing :border-box
          vertical-align :top
          border-radius :50%
          background :#141d27
          .num
            position :absolute
            top :0
            right :0
            width :24px
            height :16px
            line-height :16px
            text-align :center
            border-radius :50%
            font-size :9px
            font-weight :700
            color :#fff
            background :rgb(240,20,20)
            box-shadow :0 4px 8px 0 rgb(0,0,0,0.4)
          .logo
            width :100%
            height :100%
            border-radius :50%
            background :#2b343c
            text-align :center
            &.highlight
              background :rgb(0,160,220)
            .icon-shopping_cart
              line-height :44px
              font-size :24px
              color:#80858a
              &.highlight
                color :#fff
        .price
           display :inline-block
           vertical-align :top
           margin-top :12px
           line-height :24px
           font-size :18px
           padding-right :12px
           box-sizing :border-box
           border-right :1px solid rgba(255,255,255,0.1)
           font-weight :700
           color :#919396
           &.highlight
              color :#fff
        .desc
           display :inline-block
           margin:12px 0 0 12px
           line-height :24px
           font-size :10px
           color :#919396
      .content-right
        flex :0 0 auto
        width :105px
        background :#2b333b

        .pay
          height :48px
          line-height :48px
          text-align :center
          font-weight :700
          font-size :12px
          color :#919396
          &.unenough
            background :#2b333b
          &.enough
            background :#00b43c
            color :#fff
    .ball
      position :fixed
      left :32px
      bottom :22px
      z-index:200
      transition :all 0.6s cubic-bezier(0.49,-0.29,0.75,0.41)
      .inner
          background :#00a0dc
          width :16px
          height :16px
          transition :all 0.6s linear
          border-radius :50%
    .shopCart-list
      position :absolute
      top :0
      left :0
      z-index :-1
      width :100%
      transform: translate3d(0, -100%, 0)
      &.fold-enter-active, &.fold-leave-active
        transition: all 0.5s
      &.fold-enter, &.fold-leave-active
        transform: translate3d(0, 0, 0)
      .list-header
        height :40px
        line-height :40px
        padding :0 18px
        background :#f3f5f7
        border-bottom :1px solid rgba(7,17,27,0.1)
        .title
          float :left
          font-size :14px
          color :rgb(7,17,27)
        .empty
          float :right
          font-size :12px
          color :rgb(0,160,220)

      .list-item
        padding :0 18px
        max-height :217px
        overflow :hidden
        background :#fff
        .food
          position :relative
          padding :12px 0
          box-sizing :border-box
          border-1px(rgba(7,17,27,0.1))
          .name
            line-height :24px
            font-size :14px
            color :rgb(7,17,27)
          .price
            position :absolute
            right :90px
            bottom :12px
            line-height :24px
            font-size :14px
            font-weight :700px
            color :rgb(240,20,20)
          .cartControlWarp
            position :absolute
            bottom :6px
            right :0px
  .list-mask
    position :fixed
    top:0
    left:0
    width :100%
    height :100%
    z-index :40
    opacity :1
    background :rgba(7,17,27,0.6)
    backdrop-filter:blur(10px)
    &.fade-enter-active,&.fade-leave-active
      transition: all 0.5s
    &.fade-enter,&.fade-leave-active
      opacity :0
      background :rgba(7,17,27,0)
</style>
