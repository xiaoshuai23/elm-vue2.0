<template>
  <transition name="move">
    <div v-show="showFlag" class="food" ref="food">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image" alt="">
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
            <span class="now">￥{{food.price}}</span>
            <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food"></cartcontrol>
          </div>
          <transition name="fade">
            <div class="buy" v-show="!food.count||food.count===0"  @click.stop.prevent="addFrist">加入购物车</div>
          </transition>
        </div>
        <split></split>
        <div class="info" v-show="food.info">
          <div class="title">商品信息</div>
          <div class="text">{{food.info}}</div>
        </div>
        <split  v-show="food.info"></split>

        <div class="rating">
          <h1 class="title">商品条件</h1>
          <ratingselect @ratingtypeSelect='selectRating' @contentToggle="toggleContent" :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"></ratingselect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" class="avatar" width='12' height='12' alt="">
                </div>

                <!--模块化编程-->
                <div class="time">
                  {{rating.rateTime | formatDate}}
                </div>
                <p class="text">
                  <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
                  {{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings||!food.ratings.length">暂无评价</div>

          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  import Vue from "vue"

  import {formatDate} from '../../common/js/date'
  import BScroll from "better-scroll"
  import cartcontrol from '../cartcontrol/cartcontrol'
  import ratingselect from '../ratingselect/ratingselect'
  import split from '../split/split'
  const POSITIVE=0;
  const NEGATIVE=1;
  const ALL=2;
    export default{
      props:{
        food:{
          type:Object
        }
      },
      components:{
        cartcontrol,
        split,
        ratingselect
      },
      filters:{
        formatDate(time){
          let date=new Date(time)
//          把通用的方法放在common中的js中来创建一个模块，
          return formatDate(date,'yyyy-MM-dd hh:mm')
        }
      },
      data() {
        return {
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
            this.showFlag=true;
            this.selectType=ALL;
             this.onlyContent=false;
            this.$nextTick(()=>{
              if(!this.scroll){
                this.scroll=new BScroll(this.$refs.food,{
                  click:true
                })
              }else{
                this.scroll.refresh();
              }
            })
          },
        hide(){
            this.showFlag=false;
        },
        addFrist(event){
          if (!event._constructed) {
            return;
          }
          Vue.set(this.food,'count',1)
        },
        needShow(type,text){
          if(this.onlyContent&&!text){
            return false;
          }
          if(this.selectType===ALL){
            return true;
          }else{
            return type===this.selectType
          }
        },


        selectRating(type){
          this.selectType=type;
          this.$nextTick(()=>{
            this.scroll.refresh();
          })
        },
        toggleContent(){
          this.onlyContent=!this.onlyContent;
          this.$nextTick(()=>{
            this.scroll.refresh();
          })
        }
      }
    }
</script>

<style lang="less" rel="stylesheet/less" scoped>
  .food{
    position: fixed;
    left:0;
    top:0;
    bottom: 48px;
    z-index: 30;
    width: 100%;
    background-color: #fff;
    &.move-enter-active, &.move-leave-active {
      transition: all .3s linear;
    }
    &.move-enter, &.move-leave-active {
      transform: translateX(100%);
    }
  >.food-content{
    >.image-header{
      position: relative;
      width: 100%;
      height:0;
      /*给padding设置百分比的时候是相对于宽度来说的*/
      padding-top: 100%;
      >img{
        position: absolute;
        top:0;
        left:0;
        width: 100%;
        height: 100%;
      }
      >.back{
        position: absolute;
        top: 10px;
        left:0;
        >.icon-arrow_lift{
          display: block;
          padding: 10px;
          font-size: 20px;
          color:#fff;

        }
      }
    }
    >.content{
      padding: 18px;
      position: relative;
      >.title{
        line-height: 14px;
        margin-bottom:8px;
        font-size: 14px;
        font-weight:700;
        color:rgb(7,17,27);
      }
      >.detail{
        margin-bottom: 18px;
        line-height: 10px;
        height: 10px;
        font-size:0;
        >.sell-count{
          font-size:10px;
          color:rgb(147,153,159);
          margin-right: 12px;
        }
        >.rating{
          font-size:10px;
          color:rgb(147,153,159);
        }
      }
      >.price{
        font-weight: 700;
        line-height: 24px;
        >.now{
          margi-right:8px;
          font-size: 14px;
          color:rgb(240,20,20);
        }
        >.old{
          text-decoration: line-through;
          font-size: 10px;
          color:rgb(147,153,159);
        }
      }
      >.cartcontrol-wrapper{
        position: absolute;
        right: 12px;
        bottom: 12px;
      }
      >.buy{
        position: absolute;
        right: 18px;
        bottom: 18px;
        z-index:10;
        height: 24px;
        line-height: 24px;
        padding:0 12px;
        box-sizing: border-box;
        font-size: 10px;
        border-radius: 12px;
        background: rgb(0,160,220);
        color:#fff;
        &.fade-enter-active,.fade-leave-active{
          transition:all 0.2s linear;
        }
        &.fade-enter,.fade-leave-active{
          opacity:0;
        }

      }
    }
    >.info{
      padding: 18px;
      >.title{
        line-height: 14px;
        margin-bottom:6px;
        font-size: 14px;
        color:rgb(7,17,27);
      }
      >.text{
        line-height: 24px;
        padding:0 8px;
        font-weight:200;
        font-size: 12px;
        color:rgb(77,85,93);
      }
    }
    >.rating{
      padding-top: 18px;
      >.title{
        line-height: 14px;
        margin-left:18px;
        font-size: 14px;
        color:rgb(7,17,27);
      }
      >.rating-wrapper{
          padding:0 18px;
        >ul{
          >.rating-item{
            position: relative;
            padding:16px 0;
            border-bottom:1px solid rgba(7,17,27,.1);
            >.user{
              position: absolute;
              right:0;
              top: 16px;
              line-height: 12px;
              font-size:0;
              >.name{
                display: inline-block;
                vertical-align: top;
                font-size: 10px;
                margin-right:6px;
                color:rgb(147,153,159);
              }
              >.avatar{
                border-radius:50%;
              }
            }
            >.time{
              margin-bottom: 6px;
              line-height: 12px;
              font-size: 10px;
              color:rgb(147,153,159);
            }
            >.text{
              line-height: 16px;
              font-size: 12px;
              color:rgb(7,17,27);
              >.icon-thumb_up{
                line-height: 16px;
                font-size:12px;
                margin-right: 4px;
                color:rgb(0,160,220)
              }
              >.icon-thumb_down{
                line-height: 16px;
                font-size:12px;
                margin-right: 4px;
                color:rgb(147,153,159);
              }

            }
          }
        }
        >.no-rating{
          padding:16px 0;
          font-size:12px;
          color:rgb(147,153,159)
        }
      }

    }
  }
}


</style>
