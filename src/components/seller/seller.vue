<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <div class="title">{{seller.name}}</div>
        <div class="desc">
          <star :size="36" :score="seller.score" class="star"></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite">
          <span class="icon-favorite" :class="{'active':favorite}"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper">
          <p class="content">{{seller.bulletin}}</p>
        </div>
      </div>
      <ul v-if="seller.supports" class="supports">
        <li class="support-item" v-for="(item,index) in seller.supports">
          <span class="icon" :class="classMap[item.type]"></span>
          <span class="text">{{item.description}}</span>
        </li>
      </ul>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height='90' alt="">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title">商家信息</h1>
        <ul>
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
  import star from '../star/star'
  import split from '../split/split.vue'
  import BScroll from "better-scroll";
    export default{
      props:{
        seller:{
          type:Object
        }
      },
      components:{
        star,
        split
      },
        data() {
            return {
              favorite:false
            }
        },
      created(){
        this.classMap=['decrease','discount','special','invoice','guarantee'];
      },
      mounted:function(){
        this.$nextTick(() => {
          // 代码保证 this.$el 在 document 中
          this._initScorll();
         this._intiPics();
        });
      },
      watch: {
        "seller"() {
          this._initScorll();
          this._intiPics();
        }
      },
      methods:{
        _initScorll() {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.seller, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        },
        _intiPics(){
          if(this.seller.pics){
//            计算图片的宽度
            let picWidth=120;
            let margin=6;
            let width=(picWidth+margin)*this.seller.pics.length-margin;
            this.$refs.picList.style.width=width+"px";
            this.$nextTick(()=>{
             if(!this.picScroll){
               console.log(1);
               this.picScroll=new BScroll(this.$refs.picWrapper,{
                 scrollX:true,
                 eventPassthrough:'vertical'
               })
             }else{
               this.picScroll.refresh();
             }
            })
          }
        }
      },
      computed:{
        favoriteText(){
          return this.favorite? '已收藏':'未收藏'
        }
      }
    }
</script>

<style lang="less" rel="stylesheet/less" scoped>

.seller{
  position: absolute;
  top: 174px;
  left: 0;
  bottom:0;
  width: 100%;
  overflow: hidden;
  >.seller-content{
    >.overview{
      padding: 18px;
      position: relative;
      >.title{
        margin-bottom: 8px;
        line-height: 14px;
        font-size: 14px;
        color:rgb(7,17,27);
      }
      >.desc{
        padding-bottom:18px;
        border-bottom:1px solid rgba(7,17,27,0.1);
        font-size:0;
        >.star{
          display: inline-block;
          vertical-align: top;
          margin-right: 8px;
        }
        >.text{
          display: inline-block;
          margin-right:12px;
          vertical-align: top;
          line-height: 18px;
          font-size:10px;
          color:rgb(77,85,93);
        }
      }
      >.remark{
        display: flex;
        padding-top: 18px;
        >.block{
          flex:1;
          text-align: center;
          border-right:1px solid rgba(7,17,27,0.1);
          &:last-child{
            border:none;
          }
          >h2{
            margin-bottom:4px;
            line-height: 10px;
            font-size:10px;
            color:rgb(147,153,159);
          }
          >.content{
            line-height:24px;
            font-size:10px;
            font-weight: 200;
            color:rgb(7,17,27);
            >.stress{
              font-size: 24px;
            }
          }
        }
      }
      >.favorite{
        position: absolute;
        top:18px;
        right:18px;

        >.icon-favorite{

        }
        >.text{


        }
      }
    }

    >.bulletin{
      padding:18px 18px 0 18px;
      >.title{
        margin-bottom: 8px;
        line-height: 14px;
        font-size: 14px;
        color:rgb(7,17,27);
      }
      >.content-wrapper{
        padding:0 12px 16px 12px;
        >.content{
          line-height: 24px;
          font-size: 12px;
          color:rgb(240,20,20);
          font-weight:200;
        }
      }
    }
    >.supports{
      padding:0 16px;
      >.support-item{
        border-top:1px solid rgba(7,17,27,0.1);
        padding:16px 12px;
        font-size:0;
        >.icon{
          display:inline-block;
          vertical-align:top;
          width: 16px;
          height: 16px;
          margin-right: 6px;
          background-size:12px;
          background-repeat:no-repeat;
          &.decrease{
            background-image: url(decrease_4@2x.png);
          }
          &.discount{
            background-image: url(discount_4@2x.png);
          }
          &.guarantee{
            background-image: url(guarantee_4@2x.png);
          }
          &.invoice{
            background-image: url(invoice_4@2x.png);
          }
          &.special{
            background-image: url(special_4@2x.png);
          }
        }
        >.text{
          line-height: 16px;
          font-size: 12px;
          font-weight: 200;
          color:rgb(7,17,27);
        }
      }
    }
    >.pics{
      padding: 18px;
      >.title{
        margin-bottom: 12px;
        line-height: 14px;
        font-size: 14px;
        color:rgb(7,17,27);
      }
      >.pic-wrapper{
        width: 100%;
        overflow: hidden;
        white-space: nowrap;
        >.pic-list{
          font-size:0;
          >.pic-item{
            display: inline-block;
            margin-right:6px;
            width: 120px;
            height: 90px;
            &:last-child{
              margin-right:0;
            }
            >img{

            }
          }
        }
      }
    }
    >.info{
      padding:18px 18px 12px 18px;
      >.title{
        margin-bottom: 12px;
        line-height: 14px;
        font-size: 14px;
        color:rgb(7,17,27);
      }
      >ul{
        >.info-item{
          padding:16px 12px;
          line-height: 16px;
          border-top:1px solid rgba(7,17,27,.1);
          font-size: 12px;
          color:rgb(7,17,27);
        }
      }
    }
  }
}
</style>
