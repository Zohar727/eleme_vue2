<template lang="html">
    <div class="cartControl">
      <transition name="move">
      <div class="cartDecrease" v-show="food.count>0" @click.stop.prevent="decCart">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
      </transition>
      <div class="cartCount" v-show="food.count>0">{{food.count}}</div>
      <div class="cartAdd icon-add_circle" @click.stop.prevent="addCart"></div>
    </div>
</template>
<script>
    import Vue from 'vue'
    export default{
        props:{
            food:{
                type:Object
            }
        },
        created(){
            //console.log(this.food)
        },
        methods:{
            addCart(event){
                //判断点击事件是不是better-scroll组件触发的
                if(!event._constructed){
                    return;
                }
              //console.log('click');
                if(!this.food.count){
                    Vue.set(this.food,'count',1)
                    //this.food.count = 1;
                }else {
                    this.food.count++
                }
                //派发一个事件传递dom给父组件
                this.$emit('add',event.target);
            },
            decCart(event){
                if(!event._constructed){
                    return;
                }
                this.food.count--;
            }
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  .cartControl
    font-size :0
    .cartDecrease
      display :inline-block
      padding :6px
      opacity :1
      transition :translate3d(0,0,0)
      .inner
        display :inline-block
        line-height :24px
        font-size :24px
        color :rgb(0,160,220)
        transition :all 0.4s linear
        transform :rotate(0)
      &.move-enter-active, &.move-leave-active
        transition: all 0.4s linear
      &.move-enter, &.move-leave-active
        opacity: 0
        transform: translate3d(24px, 0, 0)
        .inner
          transform: rotate(180deg)
    .cartCount
      display :inline-block
      vertical-align :top
      width :12px
      padding-top :6px
      line-height :24px
      text-align :center
      font-size :10px
      color :rgb(147,153,159)
    .cartAdd
      display :inline-block
      padding :6px
      line-height :24px
      font-size :24px
      color :rgb(0,160,220)

</style>
