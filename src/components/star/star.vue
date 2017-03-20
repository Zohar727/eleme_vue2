<template lang="html">
    <div class="star" :class="starType">
      <span v-for="itemClass in itemClasses" :class="itemClass" class="star-item"></span>
    </div>
</template>
<script>
  //定义星星总数
  const LENGTH = 5;
  //定义星星状态
  const CLS_ON = 'on';
  const CLS_HALF = 'half';
  const CLS_OFF = 'off';
    export default{
        props:{
            size:{
                type:Number
            },
            score:{
                type:Number
            }
        },
        computed:{
            starType(){
                return 'star-'+this.size;
            },
            itemClasses(){
                let result = [];
                let score = Math.floor(this.score * 2)/2;
                let hasDecimal = score % 1 !==0;
                //integer整颗星的数目
                let integer = Math.floor(score);
                for(let i=0;i<integer;i++){
                    result.push(CLS_ON)
                }
                //判断是否有小数0.5则添加半颗星
                if(hasDecimal){
                    result.push(CLS_HALF)
                }
                //判断是否有空星
                while (result.length < LENGTH){
                    result.push(CLS_OFF)
                }
                return result;
            }
        }
    };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  .star
    font-size :0
    .star-item
      display :inline-block
      background-repeat :no-repeat
    &.star-48
      .star-item
        width :20px
        height :20px
        margin-right :22px
        background-size :20px 20px
        &:last-child
          margin-right :0
        &.on
          bg-img('star48_on')
        &.half
          bg-img('star48_half')
        &.off
          bg-img('star48_off')
    &.star-36
      .star-item
        width :15px
        height :15px
        margin-right :6px
        background-size :15px 15px
        &:last-child
          margin-right :0
        &.on
          bg-img('star36_on')
          background-color :#fff
        &.half
          bg-img('star36_half')
        &.off
          bg-img('star36_off')
    &.star-24
      .star-item
        width :10px
        height :10px
        margin-right :3px
        background-size :10px 10px
        &:last-child
          margin-right :0
        &.on
          bg-img('star24_on')
        &.half
          bg-img('star24_half')
        &.off
          bg-img('star24_off')

</style>
