<template>
    <div id="Footer">
        <div class="footer">
            <div class="foot-top clearfix">
                <div class="foot-top-left">
                  <!-- <img src="../../../static/imgs/logo.png" alt=""> -->
                </div>
                <div class="foot-top-in">
                    <ul>
                        <li>
                            <router-link to='/about/1' target="_blank">关于我们</router-link>
                        </li>
                        <li>
                            <router-link to='/about/2' target="_blank">服务条款</router-link>
                        </li>
                    </ul>
                <!-- <div class="box"><div id="ios"></div><p>Ios下载地址</p></div> -->

                </div>
                <div class="foot-top-in">
                    <ul>
                        <li>
                            <router-link to='/about/3' target="_blank">法律声明</router-link>
                        </li>
                        <li>
                            <router-link to='/about/4' target="_blank">隐私声明</router-link>
                        </li>
                    </ul>
                <!-- <div class="box"><div id="Android"></div><p>Android下载地址</p></div> -->

                </div>
                <div class="foot-top-right">
                    <span>联系我们</span>
                    <div class="foot-tel clearfix">
                        <span>中文地址:</span>
                        <em>菲律宾马尼拉大都会BGC商务中心</em>
                    </div>
                    <div class="foot-tel clearfix">
                        <span>英文地址:</span>
                        <em>Bonifacio,Metro Manila,Philippines.</em>
                    </div>
                    <div class="foot-tel">
                        <i class="iconfont icon-weibiaoti-1"></i>
                        <span>官方邮箱:</span>
                        <em>blockchainbtc@yeah.net</em>
                    </div>
                </div>
            </div>
            <div class="foot-down">
                <div class="friendship">
                    <span>友情链接:</span>
                    <a href="#">金色财经</a>
                    <a href="#">和讯网</a>
                    <a href="#">区块网</a>
                    <a href="#">币视界</a>
                    <a href="#">非小号</a>
                    <a href="#">比特币中国</a>
                    <a href="#">巴比特</a>
                </div>
                <p>Copyright (c) 2018 BCT Group All Rights Reserved</p>
            </div>
        </div>
    </div>
</template>

<script type="">
import QRCode from 'qrcodejs2'

export default {
    name: "",
    data() {
        return {};
    },
    components: {},
    created () {
        // this.$axios.post('api/user/download_link').then(res=>{
        //     new QRCode('ios', {  
        //         width: 100,  // 设置宽度 
        //         height: 100, // 设置高度
        //         text: res.data.data.ios
        //     })
        //     new QRCode('Android', {  
        //         width: 100,  // 设置宽度 
        //         height: 100, // 设置高度
        //         text: res.data.data.android
        //     })
        // })
    }
};
</script>

<style scoped lang="less">

#Footer{
    background: #292a32;
    .footer{
        width: 1200px;
        margin: 0 auto;
        padding-top: 48px;
        .foot-top{
            border-bottom: 1px solid #4e5768;
            padding-bottom: 40px;
            .foot-top-left{
                padding: 70px 50px 0 0;
                float: left;
                > img{
                    width: 224px;
                }
            }
            .foot-top-in{
                float: left;
                padding: 0 78px;
                font-size: 15px;
                color: #a4a6ab;
                line-height: 38px;
                li > a{
                    color: #a4a6ab;
                }
            }
            .foot-top-right{
                float: left;
                padding-left: 56px;
                font-size: 15px;
                color: #616978;
                line-height: 38px;
                .foot-tel > span{
                    float: left;
                    padding-right: 5px;
                }
                .foot-tel > em{
                    float: left;
                }
                .foot-tel > i{
                    font-size: 20px;
                    float: left;
                    padding-right: 5px;
                }
            }
        }
        .foot-down{
            text-align: center;
            padding-bottom: 58px;
            .friendship{
                color: #616978;
                padding-top: 26px;
                font-size: 14px;
                > a{
                    color: #616978;
                    padding: 0 8px;
                    display: inline-block;
                }
            }
            p{
                padding-top: 30px;
                font-size: 15px;
                color: #616978;
            }
        }
    }
}
</style>
