<template>
 <div>
   <div class="shopcart">
     <div class="content" @click="toggleList">
       <div class="content-left">
         <div class="logo-wrapper">
           <div class="logo" :class="{'highlight':totalCount>0}">
             <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
           </div>
           <div class="num" v-show="totalCount>0">{{totalCount}}</div>
         </div>
         <div class="price" :class="{'highlight':totalPrice>0}">￥{{totalPrice}}</div>
         <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
       </div>

       <!--存在事件冒泡，vue中阻止事件冒泡加修饰符.stop-->
       <div class="content-right" @click.stop.prevent='pay'>
         <div class="pay" :class="payClass">{{payDesc}}</div>
       </div>
       <div class="ball-container">
         <div v-for="ball in balls" v-show="ball.show" class="ball" transition="drop">
           <div class="inner inner-hook"></div>
         </div>
       </div>

     </div>
     <transition name="fold">
       <div class="shopcart-list" v-show="listShow">
         <div class="list-header">
           <h1 class="title">购物车</h1>
           <div class="emply" @click="empty">清空</div>

         </div>
         <div class="list-content" ref="listContent">
           <ul>
             <li class="food" v-for="food in selectFoods">
               <span class="name">{{food.name}}</span>
               <div class="price">
                 <span>￥{{food.price*food.count}}</span>
               </div>
               <div class="cartcontrol-wrapper">
                 <cartcontrol :food="food"></cartcontrol>
               </div>
             </li>
           </ul>
         </div>
       </div>
     </transition>
   </div>
   <transition name="fade">
     <div class="list-mask" @click="hideList" v-show="listShow">

     </div>
   </transition>
 </div>
</template>

<script>
  import Bus from '../bus'
  import BScroll from "better-scroll"
  import cartcontrol from "../cartcontrol/cartcontrol"
    export default{
      props:{
        selectFoods:{
          type:Array,
          default(){
            return [
              {
                price:10,
                count:1
              }
            ];
          }
        },
        deliveryPrice:{
          type:Number,
          default:0
        },
        minPrice:{
          type:Number,
          default:10
        }
      },
      components:{
        cartcontrol
      },
      data() {
            return {
              ballEl:null,
              balls:[
                {show:false},
                {show:false},
                {show:false},
                {show:false},
                {show:false}
              ],
              dropBall:[],
              fold:true,
            }
        },
      computed:{
        totalPrice(){
            let total=0;
            this.selectFoods.forEach((food)=>{
              total+=food.price*food.count;
            })
            return total;
          },
        totalCount(){
            let count=0;
          this.selectFoods.forEach((food)=>{
           count+=food.count;
          })
          return count;
        },
        payDesc(){
          if(this.totalPrice===0){
            return `￥${this.minPrice}元起送`
          }else if(this.totalPrice<this.minPrice){
            let diff=this.minPrice-this.totalPrice;
            return `还差￥${diff}元起送`
          }else{
            return '去结算'
          }
        },
        payClass(){
          if(this.totalPrice<this.minPrice){
            return 'not-enough'
          }else{
            return 'enough'
          }
        },
        listShow(){
          if(!this.totalCount>0){
            this.fold=true;
            return false;
          }
          let show=!this.fold;
          if(show){
            this.$nextTick(()=>{
              if(!this.scroll){
                this.scroll=new BScroll(this.$refs.listContent,{click:true})
              }else{
                this.scroll.refresh();
              }

            })
          }
          return show
        }
      },
      methods:{
        toggleList(){
          if(!this.totalCount){
            return;
          }
          this.fold=!this.fold;
        },
        empty(){
          this.selectFoods.forEach((food)=>{
            food.count=0;
          })
        },
        hideList(){
//         this.fold为true之后会触发computed中的  listShow()事件重新进行计算
          this.fold=true;
        },
        pay(){
          if(this.totalPrice<this.minPrice){
            return;
          }
          window.alert(`支付${this.totalPrice}`)

        }


      },

      created(){
        Bus.$on('addCart',(data)=>{


        })
      }





    }
</script>

<style lang="less" rel="stylesheet/less" scoped>
  @import "../../common/css/icon.css";
  .shopcart{
    position: fixed;
    left:0;
    bottom:0;
    z-index: 50;
    width: 100%;
    height: 48px;
    >.content{
      display: flex;
      background: #141d27;
      font-size:0;
      color:rgba(255,255,255,0.4);
      >.content-left{
        flex:1;
        >.logo-wrapper{
          display: inline-block;
          vertical-align: top;
          position: relative;
          top: -10px;
          margin:0 12px;
          padding: 6px;
          width: 56px;
          height: 56px;
          box-sizing:border-box;
          border-radius: 50%;
          background: #141d27;
          >.logo{
            width: 100%;
            height: 100%;
            border-radius:50%;
            background: #2b343c;
            text-align: center;
            &.highlight{
              background:rgb(0,160,220);
            }
            >.icon-shopping_cart{
              font-size: 24px;
              line-height:44px;
              color: #80858a;
              &.highlight{
                color:#fff;
              }
            }
          }
          >.num{
            position: absolute;
            top:0;
            right: 0;
            width: 24px;
            height: 16px;
            line-height:16px;
            text-align: center;
            border-radius:16px;
            font-size: 9px;
            font-weight: 700;
            color:#fff;
            background-color:rgb(240,20,20);
            box-shadow:0 4px 8px 0 rgba(0,0,0,.4)

          }
        }

        >.price{
          display: inline-block;
          vertical-align: top;
          margin-top: 12px;
          line-height: 24px;
          padding-right: 12px;
          box-sizing:border-box;
          border-right:1px solid rgba(255,255,255,0.1);
          font-size: 16px;
          font-weight: 700;
          &.highlight{
            color:#fff;
          }
        }
        >.desc{
          display: inline-block;
          vertical-align: top;
          margin: 12px 0 0 12px;
          line-height: 24px;
          font-size: 14px;
        }
      }
      >.content-right{
        flex:0 0 105px;
        width: 105px;
        >.pay{
          height: 48px;
          line-height: 48px;
          text-align: center;
          font-size: 12px;
          font-weight: 700;
          background-color: #2b333b;
          &.not-enough{
            background-color:#2b333b;
          }
          &.enough{
            background-color: #00b43c;
            color:#fff;
          }
        }
      }
      >.ball-container{
        >.ball{
          position:fixed;
          left: 32px;
          bottom: 22px;
          z-index: 200;
          &.drop-transition{
            transition:all 0.4s linear;
            >.inner{
              width: 16px;
              height: 16px;
              border-radius:50%;
              background: rgb(0,160,220);
              transition:all 0.4s linear;
            }
          }

        }
      }

    }
    >.shopcart-list{
      position: absolute;
      bottom:0;
      left:0;
      z-index:-1;
      margin-bottom: 49px;
      height:auto;
      width: 100%;



      &.fold-enter-active, &.fold-leave-active {
        transition: all .5s
      }
      &.fold-enter, &.fold-leave-to {
        transform: translateY(100%);
        opacity: 0
      }

      >.list-header{
        height: 40px;
        line-height: 40px;
        padding:0 18px;
        background: #f3f5f7;
        border-bottom:2px solid rgba(7,17,27,0.1);
        >.title{
          float: left;
          font-size: 14px;
          color:rgb(7,17,27);
        }
        >.emply{
          float: right;
          font-size: 12px;
          color:rgb(0,160,220)

        }
      }
      >.list-content{
        padding:0 18px;
        max-height:217px;
        background: #fff;
        overflow: hidden;
        >ul{
          >.food{
            position: relative;
            padding:12px 0;
            box-sizing:border-box;
            border-bottom:1px solid rgba(7,17,27,0.1);
            >.name{
              line-height:24px;
              font-size: 14px;
              color:rgb(7,17,27);
            }
            >.price{
              position: absolute;
              right: 90px;
              bottom: 12px;
              line-height: 24px;
              font-size:14px;
              font-weight:700;
              color:rgb(240,20,20);
            }
            >.cartcontrol-wrapper{
              position: absolute;
              right: 0;
              bottom:6px;
            }
          }
        }
      }
    }
  }
  .list-mask{
    position: fixed;
    top:0;
    left:0;
    width: 100%;
    height: 100%;
    z-index: 40;
    /*模糊度*/
    backdrop-filter:blur(10px);
    background-color: rgba(7,17,27,0.6);
    &.fade-enter-active, &.fade-leave-active {
      transition: all .5s
    }
    &.fade-enter, &.fade-leave-to {
      opacity: 0
    }
  }



</style>
