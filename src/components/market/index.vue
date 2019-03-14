<template>
    <div class="wrap">
      <div class="container">
        <div class="dw-title">
          行情
        </div>
        <div class="dw-body">
          <div class="lists" v-show="!marketDataShow">
            <ul class="lists-title">
              <li class="clear">
                <div class="tradeCode">交易对</div>
                <div class="latestPrice">最新价</div>
                <div class="diff">涨幅</div>
                <div class="highPrice">最高价</div>
                <div class="lowPrice">最低价</div>
                <div class="volume">24H量</div>
                <div class="turnover">24H成交额</div>
              </li>
              <li class="clear detailInfo" v-for="(item, index) of latestMarketData" :key="index" >
                <div class="tradeCode">{{item.exchangeCode}}/{{item.unitsCode}}</div>
                <div class="latestPrice">{{item.price | numTwo}}</div>
                <div class="diff">{{item.diffRate | numTwo}}%</div>
                <div class="highPrice">{{item.high | numTwo}}</div>
                <div class="lowPrice">{{item.low | numTwo}}</div>
                <div class="volume">{{item.totalVol | numTwo}}</div>
                <div class="turnover">{{item.totalAmount | numTwo}}</div>
              </li>
            </ul>
          </div>
          <div class="nodata" v-show="marketDataShow">
            <img src="../../../static/imgs/record.png">
            <p class="" style="margin-left:-4px;">无行情数据</p>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
    export default {
      name: "index",
      data() {
        return {
          marketDataShow: true,
          allMarketData: [],//所有行情代码数据
          newMarketCode: [],//行情代码
          latestMarketData: [],//合约最新行情数据
          _timeOut: null,
        }
      },
      created() {
        this.allMarketCode().then(res => {this.latestMarket(res)});
      },
      mounted() {
        this._timeOut = setInterval(() => {
          //获取首页中的所有行情数据
          this.latestMarket(this.newMarketCode);
        }, 3000);
      },
      methods: {
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
                this.marketDataShow = false;
                res.data.data.map(outVal => {
                  this.allMarketData.map(val => {
                    if(val.marketCode == outVal.code) {
                      this.$set(outVal,'exchangeCode',val.exchangeCode);
                      this.$set(outVal,'unitsCode',val.unitsCode);
                    }
                  })
                });
                this.latestMarketData = res.data.data;
              }else {
                this.marketDataShow = true;
              }
            }
          })
        },
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
    height: 40px;
    line-height: 40px;
    font-size: 20px;
    color: #333;
    padding: 20px 0;
  }
  .dw-body {
    background-color: #fff;
    margin-top: 30px;
    margin-bottom: 50px;
    border-radius: 4px;
  }
  .lists {
    background: #fff;
    padding-bottom: 30px;
  }

  .lists-title li {
    color: #999;
    padding: 0;
    line-height: 40px;
  }
  .lists-title li > div {
    float: left;
    border-right: 1px solid #e6e6e6;
    padding: 0 12px;
    color: #999;
    width: 171px;
    text-align: center;
    margin-top: 10px;
  }

  .detailInfo > div {
    border-right: none !important;
  }

  .clear:after {
    content: '';
    clear: both;
    display: block;
    visibility: hidden;
    height: 0;
    overflow: hidden;
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
