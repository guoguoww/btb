<template>
  <section>
    <Affix>
      <div id="Header">
        <div class="header clearfix">
          <div class="logo">
            <router-link to="/"><img src="../../../static/imgs/logo.png"></router-link>
          </div>
          <div class="nav">
            <ul class="clearfix">
              <li v-for="(i,index) in tab" :key="index" >
                <router-link :to="i.path" :class="{'active':$route.path==i.path}">
                  <span>{{i.name}}</span>
                </router-link>
              </li>
            </ul>
          </div>
          <div class="core">
            <ul class="clearfix">
              <template v-if="$cookie.get('_auth')">
                <li>
                  <router-link to="/newassets">
                    <span>资产</span>
                    <i class="iconfont icon-xialasanjiao1"></i>
                  </router-link>
                </li>
                <li class="order-font">
                  <router-link to="/c2cOrder">
                    <span>订单</span>
                    <i class="iconfont icon-xialasanjiao1"></i>
                    <div class="order-ul">
                      <ul>
                        <li>
                          <a href="#/order" class="">普通订单</a>
                        </li>
                      </ul>
                    </div>
                  </router-link>
                </li>
                <li>
                  <a href="#/advert" class="">
                    <span>交易广告</span>
                    <i class="iconfont icon-xialasanjiao1"></i>
                  </a>
                </li>
                <li>
                  <router-link to="/user" class="clear">
                    <i class="iconfont icon-geren5 portrait"></i>
                    <span>个人中心</span>
                  </router-link>
                </li>
              </template>
              <li v-if="$cookie.get('_auth')">
                <a href="#" class="register" @click="logout">退出</a>
              </li>
              <li v-if="!$cookie.get('_auth')">
                <router-link to="/login">
                  <span>登录</span>
                </router-link>
              </li>
              <li v-if="!$cookie.get('_auth')">
                <router-link to="/register">
                  <span>注册</span>
                </router-link>
              </li>
              <!--<li class="font">-->
              <!--<a href="#">-->
              <!--<span>简体中文</span>-->
              <!--<i class="iconfont icon-xialasanjiao1"></i>-->
              <!--<div class="font-ul">-->
              <!--<ul>-->
              <!--<li>简体中文</li>-->
              <!--<li>繁體中文</li>-->
              <!--<li>English</li>-->
              <!--</ul>-->
              <!--</div>-->
              <!--</a>-->
              <!--</li>-->
            </ul>
          </div>
        </div>
      </div>
    </Affix>
  </section>
</template>

<script type="">
export default {
    name: "headerc",
    data() {
        return {
             tab:[
                {
                    name:'行情',
                    path:'/market'
                },
                {
                    name:'C2C交易',
                    path:'/c2c'
                },
                {
                    name:'币币交易',
                    path:'/coin'
                },
                {
                    name:'合约交易',
                    path:'/contract'
                }

            ],
        };
    },
    components: {},
    methods: {
      logout() { //退出登录
        if(this.$cookie.get('_auth')){
          this.$cookie.clear() //清除cookie
          this.$router.push('/login') //跳转登录页
        }else{
          this.$router.push('/login')
        }
      },
    },
};
</script>

<style scoped lang="less">
.active{
    color:#ff3c00 !important;
}
#Header{
    height: 90px;
    /*background: #201a40;*/
    background: #fff;
    box-shadow: 0 6px 4px 0 #e9e9e9;
    .header{
        width: 1200px;
        margin: 0 auto;
        padding-top: 16px;
        .logo{
            float: left;
            width: 182px;
            img{
                width: 100%;
                height: 100%;
            }
        }
    }
    // 交易
    .nav{
        float: left;
        height: 60px;
        line-height: 60px;
        padding-left: 40px;
        font-size: 15px;
        li{
            float: left;
            padding: 0 30px;
            > a{
                /*color: #fff;*/
              color: #999;
            }
        }
        li:hover {
          a {
            color: #ff3c00;
          }
        }
    }
    // 订单资产啥玩意的
    .core{
        float: right;
        height: 60px;
        line-height: 60px;
        > ul > li{
            float: left;
            padding: 0 12px;
            font-size: 13px;
            cursor: pointer;
            > a{
                /*color: #fff;*/
              color: #999;
                display: inline-block;
                position: relative;
                i{
                    font-size: 12px;
                }
            }
        }
        li:hover {
          a:not(.register){
            color: #ff3c00;
          }
        }
        .order-ul{
            left: -15px;
            overflow: hidden;
        }

        .font .font-ul,
        .order-ul{
            position: absolute;
            background: #fff;
            box-shadow: 0 3px 9px 0 rgba(69, 112, 128, 0.14);
            top: 50px;
            width: 70px;
            border-radius: 4px;
            display: none;
        }
        .order-ul > ul > li{
            text-align: center;
            line-height: 34px;
            a{
                color: #404040;
            }
            .portrait{
                font-size: 20px;
                float: left;
                padding-right: 5px;
            }

        }
        .register{
            /*background: #484362;*/
            background: #ff3c00;
            color: #fff;
            display: inline-block;
            padding: 0 20px;
            height: 25px;
            line-height: 23px;
            margin-left: 5px;
            border-radius: 20px;
        }
    }
}
</style>
