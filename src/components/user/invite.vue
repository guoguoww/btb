<template>
    <div>
      <div class="inviteBanner">
        <img class="minCenter" src="../../../static/imgs/inviteBanner.jpg">
      </div>
      <!--分享链接-->
      <div class="inviteInfo">
        <div class="inviteLink">
          <div id="qrcode" class="ewm" ref="qrcode"></div>
          <!--<div class="ewm">-->
            <!--<img src="../../../static/imgs/wx.png" />-->
          <!--</div>-->
          <div class="inviteItem">
            <label>我的推荐ID: </label>{{userData.inviteCode}}
          </div>
          <div class="inviteItem">
            <label>推荐链接: </label>
            <input id="inviteLink1" name="inviteLink" readonly="" type="text" v-model="shareLink">
          </div>
          <div class="inviteItem shareBtns">
            <span class="share">分享: </span>
            <a href="javascript:void(0);" target="_blank" @click="sinaWeiBo()"><img src="../../../static/imgs/weibo.png"/></a>
            <a href="javascript:void(0);" target="_blank" @click="qZone()"><img src="../../../static/imgs/qq.png"/></a>
          </div>
        </div>
      </div>
      <ul class="shareList">
        <h5>分享用户</h5>
        <li>
          <span>账号</span>
          <span>日期</span>
          <span>返佣</span>
        </li>
        <li v-if="shareList.data > 0" v-for="item in shareList.data">
          <span>{{item.mobile}}</span>
          <span>{{item.lastTradeTime }}</span>
          <span>{{item.tradeTotal }}</span>
        </li>
        <li class="null" v-if="shareList.total === 0">
          <span>暂无分享人数</span>
        </li>
        <!--分页器-->
        <div class="page" v-if="shareList.total > pages.rows">
          <Page :total="30" :page-size="pages.rows" size="small" show-total show-elevator @on-change="pageChange" />
        </div>
      </ul>
      <!--规则-->
      <div class="inviteRule">
        <div class="container">
          <div class="inviteRule-title">
            <span class="minCenter">活动细则</span>
          </div>
          <div class="minCenter">
            <div class="inviteRule-con">
              <ul>
                <li><i>·</i>活动期间可获得佣金返利，后续将根据实际情况对佣金比例进行调整。</li>
                <li><i>·</i>一旦您推荐的人成功完成交易，佣金就会立刻返到您的BCT账户。</li>
                <li><i>·</i>BCT保留随时对返佣活动规则进行调整的权利，但是对您推荐的好友数量没有限制。</li>
                <li><i>·</i>被推荐人必须使用您的推荐链接、二维码或者推荐ID注册才可以。</li>
                <li><i>·</i>BCT会严查重复的或者虚假账户，一经发现，将不会支付返佣。重复账户或者共享资金是不合格的。</li>
              </ul>
              <div class="spe">
                <strong>* 特别注意：</strong>
                <p>由于市场环境的改变，欺诈风险的存在等原因，BCT保留随时对返佣规则作出调整的最终解释权。</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
  import QRCode from 'qrcodejs2'
  export default {
    name: "invite",
    data() {
      return {
        userData: {},
        shareLink: '',
        shareList: { //分享列表
          data: [],
          total: 0,
        },
        pages: { //分页
          start: 0,
          rows: 5,
        },
      }
    },
    created() {
      this.getUserData();
      this.getPromotion();
    },
    methods: {
      getUserData() {  //获取用户信息
        this.$axios.post("/api/user/query_active_user")
          .then(res => {
            if(res.data.errorCode === 0) {
              this.userData = res.data.data;
              this.shareLink = location.origin + location.pathname + '#/register/' + this.userData.inviteCode;
              this.qrcode()
            }
          })
      },
      qrcode() { //生成分享二维码
        let qrcode = new QRCode(document.getElementById("qrcode"), {
          width: 120,  // 设置宽度
          height: 120, // 设置高度
          text: this.shareLink
        })
      },
      // 分享
      qZone(){ //QQ
        var p = {
          url: document.location,
          desc: '这篇文章不错,分享一下~~',/*默认分享理由(可选)*/
          summary: document.title,/*分享摘要(可选)*/
          title: document.title,/*分享标题(可选)*/
          site: 'GMQ',/*分享来源 如：腾讯网(可选)summary*/
        };
        var s = [];
        for (var i in p) {
          s.push(i + '=' + encodeURIComponent(p[i] || ''));
        }
        var target_url = "https://connect.qq.com/widget/shareqq/index.html?" + s.join('&');
        window.open(target_url, 'qZone');
      },
      sinaWeiBo(){ //微博
        var param = {
          url: document.location,
          title: document.title, /** 分享的文字内容(可选，默认为所在页面的title)*/
          rnd: new Date().valueOf()
        }
        var temp = [];
        for( var p in param ){
          temp.push(p + '=' +encodeURIComponent( param[p ] || '' ) )
        }
        var target_url = "http://service.weibo.com/share/share.php?" + temp.join('&');
        window.open(target_url, 'sinaweibo');
      },

      // 获取分享列表
      getPromotion() {
        this.$axios({
          url: '/api/assets/promotion/details',
          method: 'post',
          data: this.pages,
        }).then(res => {
          if(!res.data.errorCode) {
            this.shareList.data = res.data.page.data;
            this.shareList.total = res.data.page.total;
          }
        })
      },
      // 分页
      pageChange(page) { //支付列表分页
        this.pages.start = this.pages.rows * (page-1);
        this.getPromotion()
      },
    },
  }
</script>

<style scoped lang="less">
  .inviteBanner {
    width: 100%;
    min-width: 1200px;
    height: 420px;
    position: relative;
    overflow: hidden;
  }
  .inviteBanner .minCenter {
    position: absolute;
    left: 50%;
    top: 50%;
    -webkit-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
  }
  /*分享链接*/
  .inviteInfo {
    min-width: 1200px;
    margin: 0 auto;
    padding: 10px 0;
  }
  .inviteLink {
    width: 545px;
    min-height: 120px;
    padding-left: 150px;
    margin: 0 auto;
    position: relative;
  }
  .inviteLink .ewm {
    width: 120px;
    height: 120px;
    padding: 4px;
    background: #fff;
    position: absolute;
    left: 0;
    top: 0;
  }
  .inviteLink .ewm img {
    width: 100%;
  }
  .inviteLink .inviteItem {
    padding: 15px 0;
    font-size: 14px;
    color: #333;
  }
  .inviteLink .inviteItem input {
    display: inline-block;
    vertical-align: middle;
    width: 320px;
    font-size: 14px;
    color: #333;
    background: #fff;
    border: 1px solid #e6e6e6;
    padding: 9px 10px;
    margin-top: 8px;
  }
  .inviteLink .inviteItem a {
    display: inline-block;
    width: 26px;
    height: 26px;
    border-radius: 100%;
    text-align: center;
    line-height: 26px;
    border: 1px solid #ddd;
    font-size: 14px;
    color: #666;
    margin-left: 10px;
    vertical-align: middle;
  }
  .inviteLink .inviteItem a img {
    width: 22px;
  }
  /*分享列表*/
  .shareList {
    width: 800px;
    margin: 10px auto;
    padding: 0 0 10px;
    border-bottom: 1px solid #ddd;
    h5 {
      text-align: center;
      font-size: 16px;
      line-height: 30px;
    }
    li {
      line-height: 36px;
      span {
        display: inline-block;
        width: calc((100% - 9px) / 3);
        text-align: center;
      }
    }
    .null {
      text-align: center;
      line-height: 50px;
    }
    /*分页*/
    .page {
      text-align: center;
      padding: 10px 0;
      border-top: 1px solid #ededed;
    }
  }
    /*规则*/
  .inviteRule {
    min-width: 1200px;
    min-height: 290px;
    padding: 60px 0;
    position: relative;
  }
  .container {
    width: 1200px;
    margin: 0 auto;
    padding: 0;
  }
  .inviteRule-title {
    width: 1200px;
    height: 8px;
    position: relative;
  }
  .inviteRule-title span {
    padding: 10px 50px;
    font-size: 16px;
    color: #666;
  }
  .minCenter {
    position: absolute;
    left: 50%;
    top: 50%;
    -webkit-transform: translate(-50%, -50%);
    transform: translate(-50%, -50%);
  }
  .inviteRule-con{
    font-size: 14px;
    color: #666;
    margin-top: 60px;
  }
  .inviteRule-con ul {
    margin: 15px 0 30px;
  }
  .inviteRule-con ul li {
    line-height: 1.5;
    position: relative;
    padding-left: 20px;
  }
  .inviteRule-con ul li i {
    position: absolute;
    left: 0;
    top: -2px;
    font-size: 16px;
    font-weight: bold;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    margin-right: 5px;
  }
  .inviteRule-con .spe strong {
    color: #666;
    font-size: 14px;
    font-weight: normal;
  }
  .inviteRule-con .spe p {
    margin-top: 15px;
  }
</style>
