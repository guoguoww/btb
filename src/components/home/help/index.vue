<template>
  <div class="wrap">
    <div class="container">
      <div class="dw-title">
        帮助中心
      </div>
      <div class="dw-body">
        <ul>
          <router-link :to="{path: '/helpDetail',query: {queryId: item.id}}" tag="li" v-for="(item,index) of helpInfo" :key="index"><span>{{item.title}}</span><span>{{item.createTime}}</span></router-link>
        </ul>
        <div class="nodata" v-show="helpOutShow">
          <img src="../../../../static/imgs/record.png">
          <p class="" style="margin-left:-4px;">无帮助中心记录</p>
        </div>
      </div>
    </div>
    <div style="text-align: center;margin-bottom: 50px;padding-bottom: 20px" v-show="pageShow">
      <Page :total="Number(totalList)" :page-size="10" size="small" show-total  @on-change="togglePage"/>
    </div>
  </div>
</template>

<script>
  export default {
    name: "index",
    data() {
      return {
        reqParams: {
          start: 0,
          rows: 10,
        },
        helpInfo: [],
        helpOutShow: true,
        totalList: '',//总条数
        pageShow: false,//是否显示分页
      }
    },
    mounted() {
      this.helpList();
    },
    methods: {
      helpList() {
        this.$axios.post('/api/user/help/center',{start: this.reqParams.start, rows: this.reqParams.rows}).then(res => {
          if(res.data.errorCode == 0) {
            if(res.data.page.data.length != 0) {

              this.helpInfo = res.data.page.data;
              this.helpOutShow = false;
              this.totalList = res.data.page.total;

              if(this.totalList > 10){
                this.pageShow = true;
              }else {
                this.pageShow = false
              }

            }
          }
        })
      },
      togglePage(pageNum){//切换分页时的点击事件
        this.reqParams.start = (pageNum-1)*10;
        this.helpList();
      }
    }
  }
</script>

<style scoped>
  .wrap {
    background: #f5f4fc;
    min-width: 1200px;
  }
  .container {
    width: 1200px;
    margin: 0 auto;
    padding: 0;
  }
  .dw-title {
    width: 100%;
    height: 50px;
    line-height: 50px;
    font-size: 18px;
    color: #333;
  }
  .dw-body {
    background-color: #fff;
    border-radius: 10px !important;
    margin-bottom: 50px;
  }
  .dw-body > ul {
    padding: 38px 58px;
    width: 80%;
    margin: auto;
  }
  .dw-body > ul > li {
    height: 50px;
    line-height: 50px;
    cursor: pointer;
  }
  .dw-body > ul > li span:nth-of-type(1) {
    float: left;
    width: 600px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .dw-body > ul > li span:nth-of-type(2) {
    float: right;
  }
  .nodata {
    text-align: center;
    padding: 20px;
  }
  .nodata img {
    width: 60px;
  }
  .nodata p {
    font-size: 14px;
    color: #999;
    margin: 10px 0 0 10px;
    line-height: 100%;
  }
</style>

