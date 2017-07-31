<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease icon-remove_circle_outline" v-show="food.count>0" @click.stop.prevent="decreaseCart"></div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
  </div>
</template>

<script>
  import Vue from 'vue'
  import Bus from '../bus'
    export default{
      props:{
        food:{
          type:Object
        }
      },
        data() {
            return {}
        },
      methods:{
        addCart(event){
            if(!event._constructed){
              return;
            }
            if(!this.food.count){
              Vue.set(this.food,'count',1)
            }else{
              this.food.count++;
            }
//            console.log(event.target);
//            this.$emit('cardAdd',event.target)
            Bus.$emit('addCart',event.target)
          },
        decreaseCart(){
          if(!event._constructed){
            return;
          }
          if(this.food.count){
            this.food.count--;
          }
        }
      }
    }
</script>

<style lang="less" rel="stylesheet/less" scoped>
  .move-enter, .move-leave-to {
    transform: translateX(24px);
    opacity: 0;
  }

  .move-enter-active {
    transition: all .4s linear;
  }
  .move-leave-active {
    transition: all .4s linear;
  }

  .cartcontrol{
    font-size: 0;
    >.cart-decrease{
      opcity:0;
      transform:translate3D(0,0,0);
      display: inline-block;
      padding: 6px;
      font-size: 24px;
      line-height: 24px;
      color:rgb(0,160,220);
    }
    >.cart-count{
      display: inline-block;
      vertical-align: top;
      width: 12px;
      padding-top: 6px;
      line-height:24px;
      text-align: center;
      font-size: 10px;
      color:rgb(147,153,159);
    }
    >.cart-add{
      display: inline-block;
      padding: 6px;
      line-height: 24px;
      font-size: 24px;
      color:rgb(0,160,220);
    }
  }
</style>
