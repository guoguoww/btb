<template>
    <div>
      <swiper :options="swiperOption" ref="mySwiper">
        <!-- slides -->
        <swiper-slide v-for="(item, index) of banners" :key="index"><img :src="item.url" alt=""></swiper-slide>
        <!-- Optional controls -->
        <!--<div class="swiper-pagination"  slot="pagination"></div>-->
      </swiper>
      <div class="homeInfo">
        <ul>
          <li v-for="(item, index) of latestMarketData" :key="index" v-if="index < 4">
            <p><span>{{item.exchangeCode}}/{{item.unitsCode}}</span><span :class="[item.diffRate>0?'up':'down']">{{item.diffRate | numTwo}}%</span></p>
            <p><span>{{item.price | numTwo}}</span><span>≈{{item.symbolCny | numTwo}} CNY</span></p>
            <p><span>24H量  </span><span>{{item.totalAmount | numTwo}}</span></p>
          </li>
        </ul>
        <div class="newsList">
          <div class="announce">
            <p><label>公告列表</label><router-link to="/announce" tag="span">更多</router-link></p>
            <ul>
              <router-link :to="{path: '/announceDetail',query: {queryId: item.id}}" tag="li" v-for="(item, index) of announceInfo" :key="index" v-if="index < 6"><span>{{item.title}}</span><span>{{item.createTime}}</span></router-link>
            </ul>
          </div>
          <div class="helpCenter">
            <p><label>帮助中心</label><router-link to="help" tag="span">更多</router-link></p>
            <ul>
              <router-link :to="{path:'/helpDetail',query: {queryId: item.id}}" tag="li" v-for="(item, index) of helpInfo" :key="index" v-if="index < 6"><span>{{item.title}}</span><span>{{item.createTime}}</span></router-link>
            </ul>
          </div>
        </div>
        <div class="platformInfo">
          <div class="platformTitle">
            <p>全平台终端接入</p>
            <p>ios、Android、Mac、Windows多个平台支持全业务功能</p>
          </div>
          <ul class="platform">
            <li>
              <img class="ewm" height="117" src="./imgs/downoadPhone.png" width="116">
            </li>
            <li>
              <p>扫码下载ios、Android版</p>
              <p>抢先体验内测版、功能更多更全</p>
            </li>
            <li>
              <img alt="" src="./imgs/mac.png">
              <p>MAC版下载</p>
            </li>
            <li>
              <img alt="" src="./imgs/windows.png">
              <p>WIN版下载</p>
            </li>
          </ul>
        </div>
      </div>
      
    <div class="suspend" @click='a'>
	<dl>
		<dt class="IE6PNG"></dt>
		<dd class="suspendQQ"><a href="http://shang.qq.com/wpa/qunwpa?idkey=cb91b633f1db596a948828f4f333dbb7394ceda014c958cf8338bb2737cb9496" target="_blank"></a></dd>
		<dd class="suspendTel"><a href="javascript:void(0);"></a></dd>
	</dl>
</div>
    </div>
</template>

<script>
    export default {
      name: "index",
      data() {
        return {
          banners: [
            {url: require('./imgs/banner1.jpg')},
            {url: require('./imgs/banner2.jpg')},
            {url: require('./imgs/banner3.jpg')},
          ],
          swiperOption: {
            // 如果需要分页器
            pagination: {
              el: '.swiper-pagination',
            },
            loop:true,
            //设定初始化时slide的索引
            initialSlide:0,
            //自动播放
            autoplay:true,
            //滑动速度
            speed:800,
            //小手掌抓取滑动
            grabCursor : true,
          },
          announceInfo: [],//首页公告列表数据
          helpInfo: [],//帮助中心列表数据
          allMarketData: [],//所有行情代码数据
          newMarketCode: [],//行情代码
          latestMarketData: [],//合约最新行情数据
          _timeOut: null,
        }
      },
      computed: {
        swiper() {
          return this.$refs.mySwiper.swiper
        }
      },
      created() {
        this.allMarketCode().then(res => {this.latestMarket(res)});
      },
      mounted() {
        this.swiper.slideTo(1, 1000, false);
        this.announceList();
        this.helpList();
        this._timeOut = setInterval(() => {
          //获取首页中的所有行情数据
          this.latestMarket(this.newMarketCode);
        }, 3000);
      },
      methods: {
        a(){
          window.open('https://tb.53kf.com/code/client/10196276/1')
        },
        allMarketCode() {//获取所有行情代码
          return this.$axios.post('/api/market/all_market').then(res => {
            if(res.data.errorCode == 0) {
              if(res.data.data.length != 0) {

                this.allMarketData = res.data.data;

                res.data.data.map(val => {
                  this.newMarketCode.push(val.marketCode);
                })
                return this.newMarketCode.join();
              }
            }
          })
        },
        latestMarket(codes) {//获取合约最新行情
          this.$axios.get(`/api/market/latest_market?codes=${codes}`).then(res => {
            if(res.data.errorCode == 0) {
              if(res.data.data.length != 0) {
                res.data.data.map(outVal => {
                  this.allMarketData.map(val => {
                    if(val.marketCode == outVal.code) {
                      this.$set(outVal,'exchangeCode',val.exchangeCode);
                      this.$set(outVal,'unitsCode',val.unitsCode);
                    }
                  })
                });
                this.latestMarketData = res.data.data;
              }
            }
          })
        },
        announceList() {//公告接口
          this.$axios.post('/api/user/announce',{start: 0, rows: 10}).then(res => {
            if(res.data.errorCode == 0) {
              if(res.data.page.data.length != 0) {
                this.announceInfo = res.data.page.data;
              }
            }
          })
        },
        helpList() {//帮助中心接口
          this.$axios.post('/api/user/help/center',{start: 0, rows: 10}).then(res => {
            if(res.data.errorCode == 0) {
              if(res.data.page.data.length != 0) {
                this.helpInfo = res.data.page.data;
              }
            }
          })
        }
      },
      filters: {
        //保留2位小数点过滤器 不四舍五入
        numTwo(value) {
          var toFixedNum = Number(value).toFixed(3);
          var realVal = toFixedNum.substring(
            0,
            toFixedNum.toString().length - 1
          );
          return realVal;
        },
      },
      beforeDestroy() {
        clearInterval(this._timeOut);
        this._timeOut = null;
      },
    }
</script>

<style scoped>
.suspend {
    width: 40px;
    height: 198px;
    position: fixed;
    top: 400px;
    right: 0;
    overflow: hidden;
    z-index: 9999;
}
.suspend dl {
    width: 120px;
    height: 198px;
    border-radius: 25px 0 0 25px;
    padding-left: 40px;
    box-shadow: 0 0 5px #e4e8ec;
}
.suspend dl dt {
    width: 40px;
    height: 198px;
    background: url(http://www.17sucai.com/preview/1/2014-08-14/%E5%9C%A8%E7%BA%BFqq%E5%AE%A2%E6%9C%8D/images/suspend.png);
    position: absolute;
    top: 0;
    left: 0;
    cursor: pointer;
}
  .homeInfo {
    width: 1200px;
    margin: 45px auto;
    background: #fff;
  }
  .homeInfo > ul {
    width: 100%;
    overflow: hidden;
    height: 155px;
    padding-top: 25px;
  }

  .homeInfo > ul li {
    color: #e8b342;
    border-radius: 10px;
    background-color: rgb(255, 255, 255);
    box-shadow: 0px 0px 10px 0px rgba(9, 2, 4, 0.1);
    width: 280px;
    height: 99px;
    float: left;
    margin: 0 9px;
    overflow: hidden;
    margin-top: 10px;
  }
  .homeInfo > ul li p {
    margin-left: 20px;
  }
  .homeInfo > ul li p:first-child span:first-child {
    font-weight: 100 !important;
    font: 20px/30px "\5FAE\8F6F\96C5\9ED1";
  }
  .homeInfo > ul li p:first-child span:last-child {
    display: inline-block;
    font: 12px/20px "\5FAE\8F6F\96C5\9ED1";
    color: #fff;
    width: 50px;
    text-align: center;
    background: #ff3c00;
    border-radius: 5px;
    margin-left: 35px;
  }
  .homeInfo > ul li p:nth-child(2) span {
    font: 20px/35px "\5FAE\8F6F\96C5\9ED1";
  }
  .homeInfo > ul li p:nth-child(2) span:last-child {
    display: inline-block;
    margin-left: 20px;
  }
  .homeInfo > ul li p:nth-child(3) span {
    font: 18px/30px "\5FAE\8F6F\96C5\9ED1";
    font-weight: 100 !important;
  }
  .homeInfo > ul li p:nth-child(3) span:last-child {
    display: inline-block;
    font-size: 16px;
    margin-left: 60px;
  }
  .homeInfo > .newsList {
    width: 100%;
    overflow: hidden;
  }
  .newsList:after {
    content: '';
    clear: both;
  }
  .newsList > div {
    float: left;
    width: 50%;
    margin-top: 60px;
    padding: 20px;
    height: 330px;
  }
  .newsList > div:first-child {
    border-right: 1px solid #ddd;
  }
  .announce > p, .helpCenter > p {
    height: 30px;
    line-height: 30px;
  }
  .announce > p label, .helpCenter > p label {
    float: left;
    font-size: 26px;
    color: #666;
  }
  .announce > p span, .helpCenter > p span {
    float: right;
    color: #ff3c00;
    cursor: pointer;
  }
  .announce > ul, .helpCenter > ul {
    margin-top: 20px;
  }
  .announce > ul li, .helpCenter > ul li {
    height: 40px;
    line-height: 40px;
    cursor: pointer;
    color: #000;
  }
  .announce > ul li span:last-child, .helpCenter > ul li span:last-child {
    float: right;
  }
  .announce > ul li span:first-child, .helpCenter > ul li span:first-child {
    float: left;
    width: 400px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .platformTitle {
    width: 510px;
    height: 130px;
    border-radius: 16px;
    background-color: rgb(255, 255, 255);
    box-shadow: 0px 0px 6.86px 0.14px rgba(9, 2, 4, 0.08);
    margin: 100px auto 0;
    padding-top: 27px;

  }
  .platformTitle > p:nth-of-type(1) {
    font: 36px/56px "\5FAE\8F6F\96C5\9ED1";
    font-weight: 700;
    text-align: center;
  }
  .platformTitle > p:nth-of-type(2) {
    font: 18px/38px "\5FAE\8F6F\96C5\9ED1";
    text-align: center;
  }
  .platform {
    overflow: hidden;
    width: 1200px;
    margin: 0 auto;
    height: 228px;
  }
  .platform > li {
    float: left;
  }
  .platform > li:nth-of-type(1) {
    padding: 38px 0 0 98px;
  }
  .platform > li:nth-of-type(2) {
    padding: 62px 96px 0 42px;
  }
  .platform > li:nth-of-type(2) > p:nth-of-type(1) {
    border-radius: 23px;
    background-color: #ff3c00;
    width: 234px;
    height: 48px;
    font: 18px/48px "\5FAE\8F6F\96C5\9ED1";
    color: #fff;
    text-align: center;
  }
  .platform > li:nth-of-type(2) > p:nth-of-type(2) {
    font: 14px/24px "\5FAE\8F6F\96C5\9ED1";
    color: #ff3c00;
    width: 234px;
    text-align: center;
  }
  .platform > li:nth-of-type(3) {
    padding: 44px 90px 0 110px;
    background: url('./imgs/line.png') no-repeat 0 50px;
    height: 100%;
    text-align: center;
  }
  .platform > li:nth-of-type(3) > p {
    font: 18px/44px "\5FAE\8F6F\96C5\9ED1";
  }
  .platform > li:nth-of-type(4) {
    padding: 55px 90px 0 124px;
    background: url('./imgs/line.png') no-repeat 0 50px;
    height: 100%;
    text-align: center;
  }
  .platform > li:nth-of-type(4) > p {
    font: 18px/44px "\5FAE\8F6F\96C5\9ED1";
  }
  .platform > li > img {
    vertical-align: middle;
  }
  .up {
    background: #f00 !important;
  }
  .down {
    background: #0a8415 !important;
  }
</style>
