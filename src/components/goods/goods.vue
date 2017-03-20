<template lang="html">
    <div class="goods">
        <div class="menu-wrap" ref="menuwrap">
          <ul>
            <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex === index}"
            @click="selectMenu(index,$event)">
              <span class="text">
                <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
                {{item.name}}
              </span>
            </li>
          </ul>
        </div>
        <div class="foods-wrap" ref="foodswrap">
          <ul>
            <li v-for="item in goods" class="food-list food-list-hook" ref="foodList">
              <h1 class="title">{{item.name}}</h1>
              <ul>
                <li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item">
                  <div class="icon">
                    <img width="57" height="57" :src="food.icon">
                  </div>
                  <div class="content">
                    <h2 class="name">{{food.name}}</h2>
                    <p class="desc">{{food.description}}</p>
                    <div class="extra">
                      <span class="count">月售{{food.sellCount}}份</span>
                      <span>好评率{{food.rating}}%</span>
                    </div>
                    <div class="price">
                      <span class="new-price">￥{{food.price}}</span>
                      <span v-show="food.oldPrice" class="old-price">￥{{food.oldPrice}}</span>
                    </div>
                    <div class="cartControl-wrap">
                      <cartControl @add="addFood" :food="food"></cartControl>
                    </div>
                  </div>
                </li>
              </ul>
            </li>
          </ul>
        </div>
        <shopCart ref="shopCart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopCart>
        <food @add="addFood" :food="selectedFood" ref="food"></food>
    </div>
</template>
<script>
  import Bscroll from 'better-scroll';
  import shopCart from '../shopCart/shopCart'
  import cartControl from '../cartControl/cartControl'
  import food from '../food/food'
  const ERR_OK =  0;
    export default{
        props:{
            seller:{
                type:Object
            }
        },
        data(){
            return {
                goods:[],
                listHeight:[],
                scrollY:0,
                selectedFood:{}
            };
        },
        computed:{
            currentIndex(){
                for(let i=0;i<this.listHeight.length;i++){
                    let height1 = this.listHeight[i];
                    let height2 = this.listHeight[i+1];
                    if(!height2 || (this.scrollY >= height1 && this.scrollY <height2)){
                        return i;
                    }
                }
                return 0;
            },
            selectFoods(){
                let foods = [];
                this.goods.forEach((good) => {
                    good.foods.forEach((food) => {
                        if(food.count){
                            foods.push(food);
                        }
                    });
                });
                return foods;
            }
        },
        created(){
            this.classMap = ['decrease','discount','special','invoice','guarantee']
//            this.$http.get('/api/goods').then(response => {
//                response = response.body;
//                if(response.errno === ERR_OK){
//                    this.goods = response.data;
//                    //console.log(this.goods);
//                    //渲染dom后再进行计算
//                    this.$nextTick(() => {
//                      this._initScroll();
//                      //计算索引高度，实现联动效果
//                      this._calculateHeight();
//                      //console.log('计算索引');
//                    });
//
//                }
//            });
            this.$http.get(`/static/goods.json`).then((response) => {
                //response = response.body;
                this.goods = response.data;
                this.$nextTick(() => {
                  this._initScroll();
                  //计算索引高度，实现联动效果
                  this._calculateHeight();
                  //console.log('计算索引');
              });
            })
        },
        methods: {
            selectMenu(index,event){
                if(!event._constructed){
                    return;
                }
                let foodList = this.$refs.foodList;
                let el = foodList[index];
                this.foodScroll.scrollToElement(el,300);
              //console.log(index);
            },
            _initScroll(){
                this.menuScroll = new Bscroll(this.$refs.menuwrap,{
                    click:true
                });
                this.foodScroll = new Bscroll(this.$refs.foodswrap,{
                    click:true,
                    probeType:3,
                });
//                this.foodsScroll.on('scroll',(pos) => {
//                    this.scrollY = Math.abs(Math.round(pos.y));
//                })
              this.foodScroll.on('scroll', (pos) => {
                this.scrollY = Math.abs(Math.round(pos.y));
                //console.log(this.scrollY);
              });
//              this.foodScroll.addEventListener('scroll',(pos) => {
//                this.scrollY = Math.abs(Math.round(pos.y));
//              })
          },
          _calculateHeight(){
                //let foodList = this.$refs.foodswrap.getElementsByClassName('food-list-hook');
                let foodList = this.$refs.foodList;
                let height = 0;
                this.listHeight.push(height);
                for(let i=0;i<foodList.length;i++){
                    let item = foodList[i];
                    height+= item.clientHeight;
                    this.listHeight.push(height);
                    //console.log(height);
                }
          },
          addFood(target){
              this._drop(target);
          },
          _drop(target){
            this.$nextTick(() => {
              this.$refs.shopCart.drop(target);
            });
          },
          selectFood(food,event){
              if(!event._constructed){
                  return;
              }
              this.selectedFood = food;
              this.$refs.food.show();
          }
        },
        components:{
            shopCart,
            cartControl,
            food
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  .goods
    display :flex
    position :absolute
    top: 174px
    bottom :46px
    width :100%
    overflow :hidden
    .menu-wrap
      flex :0 0 80px
      width :80px
      background :#f3f5f7
      .menu-item
        display :table
        width :56px
        height :54px
        padding :0 12px
        line-height :14px
        font-size :12px
        border-1px(rgba(7,17,29,0.1))
        &.current
          background :#fff
          border-none()
          .text
            font-weight :700
        .icon
          display :inline-block
          width :12px
          height :12px
          margin-right :2px
          background-size :12px 12px
          background-repeat :no-repeat
          &.decrease
            bg-img('decrease_3')
          &.discount
            bg-img('discount_3')
          &.guarantee
            bg-img('guarantee_3')
          &.invoice
            bg-img('invoice_3')
          &.special
            bg-img('special_3')
        .text
          width :56px
          display :table-cell
          vertical-align :middle

    .foods-wrap
     flex :1
     .title
       padding-left :14px
       height :26px;
       line-height :26px
       border-left :1px solid #d9dde1
       font-size :12px
       color :rgb(147,153,159)
       background :#f3f5f7
     .food-item
       display :flex
       margin :18px
       padding-bottom :18px
       border-1px(rgba(7,17,27,0.1))
       &:last-child
         border-none()
         margin-bottom :0px
         margin-top :-1px
       .icon
          flex :0 0 57px
          margin-right :10px
       .content
        flex :1
        .name
          margin :2px 0 8px 0
          height :14px
          line-height: 14px
          font-size :14px
          color :rgb(7,17,27)
        .desc
          line-height :12px
          font-size :10px
          color :rgb(147,153,159)
          margin-bottom :8px
        .extra
           line-height: 10px
           font-size :10px
           color :rgb(147,153,159)
           .count
             margin-right :8px
        .price
          line-height :24px
          font-weight :700
          .new-price
            font-size :14px
            color :rgb(240,20,20)
            font-weight :700
          .old-price
            font-size :10px
            color :rgb(143,157,159)
            font-weight :700

        .cartControl-wrap
          position :absolute
          right :0
          bottom :12px
</style>
