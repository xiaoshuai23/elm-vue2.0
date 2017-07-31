<template>
  <div class="ratings" ref='rating'>
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class='title'>服务态度</span>
            <star :size="36" :score='seller.serviceScore' class="star"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class='title'>商品评分</span>
            <star :size="36" :score='seller.foodScore' class="star"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class='title'>送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect @ratingtypeSelect='selectRating' @contentToggle="toggleContent" :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="ratings"></ratingselect>
      <div class="rating-wrapper">
         <ul>
           <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType,rating.text)">
             <div class="avatar">
               <img :src="rating.avatar" width='18' height='18' alt="">
             </div>
             <div class="content">
               <h1 class='name'>{{rating.username}}</h1>
               <div class='star-wrapper'>
                 <star :size="24" :score='rating.score' class="star"></star>
                 <span class="delivery" v-show='rating.deliveryTime'>{{rating.deliveryTime}}分钟送达</span>
               </div>
               <p class="text">{{rating.text}}</p>
               <div class="recommend" v-show="rating.recommend&&rating.recommend.length">
                 <span class="icon-thumb_up"></span>
                 <span v-for="item in rating.recommend" class="item">{{item}}</span>
               </div>
               <div class="time">
                 {{rating.rateTime | formatDate}}
               </div>
             </div>
           </li>
         </ul>
      </div>
    </div>
  </div>
</template>

<script>
  import Vue from "vue"
  import BScroll from "better-scroll"
  import star from '../star/star'
  import split from '../split/split'
  import ratingselect from '../ratingselect/ratingselect'
  import {formatDate} from '../../common/js/date'
  const ALL=2;
  const ERR_OK=0;
    export default{
      props:{
        seller:{
          type:Object
        }
      },
      components:{
        star,
        split,
        ratingselect
      },
        data() {
            return {
              ratings:[],
              showFlag:false,
              selectType:ALL,
              onlyContent:true,
              desc:{
                all:'全部',
                positive:"满意",
                negative:'不满意'
              }
            }
        },
      created(){
        this.$http.get('/api/ratings').then((res)=>{
          res=res.body;
          if(res.errno===ERR_OK){
            this.ratings=res.data;
            this.$nextTick(()=>{
              this.scroll=new BScroll(this.$refs.rating,{
                click:true
              })
            })
          }
        })
      },
      filters:{
        formatDate(time){
          let date=new Date(time)
//          把通用的方法放在common中的js中来创建一个模块，
          return formatDate(date,'yyyy-MM-dd hh:mm')
        }
      },
      methods:{
        needShow(type,text){
          if(this.onlyContent && !text){
            return false;
          }
          if(this.selectType===ALL){
            return true;
          }else{
            return type===this.selectType;
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
  .ratings{
    position: absolute;
    width: 100%;
    top: 174px;
    bottom:0;
    width: 100%;
    overflow: hidden;
    >.ratings-content{
      >.overview{
        display: flex;
        padding:18px 0;
        >.overview-left{
          flex:0 0 137px;
          padding:6px 0;
          width: 137px;
          border-right:1px solid rgba(7,17,27,0.2);
          text-align: center;
          @media only screen and (max-width:320px){
            flex:0 0 120px;
            width: 120px;
          }
          >.score{
            margin-bottom: 6px;
            line-height: 28px;
            fong-size:24px;
            color:rgb(255,153,0)
          }
          >.title{
            margin-bottom:8px;
            line-height:12px;
            font-size: 12px;
            color:rgb(7,17,27);
          }
          >.rank{
            line-height: 10px;
            font-size: 10px;
            color:rgb(147,153,159);
          }
        }
        >.overview-right{
          flex: 1;
          padding-left: 24px;
          padding-top: 6px;
          @media only screen and(max-width:320px){
            padding-left: 6px;
          }
          >.score-wrapper{
            margin-bottom: 8px;
            font-size:0;
            >.title{
              vertical-align: top;
              line-height: 18px;
              font-size: 12px;
              color:rgb(7,17,27)
            }
            >.star{
              display: inline-block;
              vertical-align: top;
              margin-left: 12px;
              @media only screen and(max-width:320px){
                margin-left: 6px;
              }
            }
            >.score{
              vertical-align: top;
              line-height: 18px;
              font-size: 12px;
              color:rgb(225,153,0);
              margin-left: 12px;
              @media only screen and(max-width:320px){
                margin-left: 6px;
              }
            }
          }
          >.delivery-wrapper{
            font-size:0;
            >.title{
              vertical-align: top;
              line-height: 18px;
              font-size: 12px;
              color:rgb(7,17,27)
            }
            >.delivery{
              margin-left: 12px;
              line-height: 18px;
              font-size: 12px;
              color:rgb(147,153,159);

            }
          }
        }
      }
      >.rating-wrapper{
       padding:0 18px;
        >ul{
          >.rating-item{
            display: flex;
            padding:18px 0;
            border-bottom:1px solid rgba(7,17,27,0.2);
            >.avatar{
              flex:0 0 28px;
              width: 28px;
              margin-right:12px;
              >img{
                border-radius:50%;
              }
            }
            >.content{
              flex:1;
              position: relative;
              >.name{
                line-height: 12px;
                font-size: 10px;
                color:rgb(7,17,27);
                margin-bottom: 4px;
              }
              >.star-wrapper{
                margin-bottom:6px;
                font-size:0;
                >.star{
                  display: inline-block;
                  vertical-align: top;
                  margin-right: 6px;

                }
                >.delivery{
                  display: inline-block;
                  vertical-align: top;
                  line-height: 12px;
                  font-size: 10px;
                  color:rgb(147,153,159);

                }
              }
              >.text{
                margin-bottom:8px;
                line-height: 18px;
                color:rgb(7,17,27);
                font-size: 12px;
              }
              >.recommend{
                line-height: 16px;
                font-size:0;
                >.icon-thumb_up{
                  display: inline-block;
                  margin:0 8px 4px 0;
                  font-size: 12px;
                  color:rgb(0,160,220);
                }
                >.item{
                  display: inline-block;
                  margin:0 8px 4px 0;
                  padding:0 6px;
                  border:1px solid rgba(7,17,27,.1);
                  border-radius:2px;
                  color:rgb(147,153,159);
                  font-size: 9px;
                }
              }
              >.time{
                position: absolute;
                top:0;
                right:0;
                line-height: 12px;
                font-size:10px;
                color:rgb(147,153,159);
              }
            }
          }
        }
      }
    }
  }
</style>
