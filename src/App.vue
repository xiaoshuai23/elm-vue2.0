<template>
  <div class="app">
    <v-header :seller="seller"></v-header>
    <div class="tab">
      <div class="tab-item">
        <router-link :to="{path:'/goods'}">商品</router-link>
      </div>
      <div class="tab-item">
        <router-link :to="{path:'/ratings'}">评论</router-link>
      </div>
      <div class="tab-item">
        <router-link :to="{path:'/seller'}">商家</router-link>
      </div>
    </div>
   <router-view :seller="seller"></router-view>
  </div>
</template>

<script>
  import vHeader from "./components/header/header"
  const ERR_OK=0;
    export default{
    data() {
      return {
        seller:{}
      };
    },
      created(){
        this.$http.get('/api/seller')
          .then((res)=>{
            res=res.body;
            if(res.errno===ERR_OK){
              this.seller=res.data;
              console.log(this.seller);
            }
          },(err)=>{

          })
      },
    components:{
      vHeader
     }
    }
</script>

<style>
  .tab{
    display:flex;
    width: 100%;
    height: 40px;
    line-height: 40px;

  }
  .tab-item{
    flex:1;
    text-align:center;
    border-bottom:1px solid rgba(7,17,27,.1)
  }
  .tab-item a{
    display: block;
    font-szie:14px;
    color:rgb(77,85,93)
  }
  .tab-item a.router-link-active{
    color:rgb(240,20,20)
  }
</style>
