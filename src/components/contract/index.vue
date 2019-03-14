<template>
    <div class="app">
        <!-- 头 -->
        <tradeheader style="margin-bottom:10px;" />
        <div class="app-in">
            <!-- 左边 -->
            <div id="Tab" class="clearfix" v-show="!hl">
                <div class="money">
                    <div class="money-cont">
                        <p>{{coin}}余额：{{money}}</p>
                        <p>总保证金：{{totalprofit.amount}}</p>
                        <p>浮动盈亏：
                            <i :class="totalprofit.profit>0?'hong':'lv'">{{totalprofit.profit}}</i>
                        </p>
                        <em class="border-lefts"></em>
                        <em class="border-rights"></em>
                        <em class="border-bottoms"></em>
                        <em class="border-tops"></em>
                    </div>
                </div>
                <div class="tab-nav">
                    <div class="nav-cont">
                        <div class="nav-head">
                            <ul class="clearfix">
                                <li :class="{'nav-active':navacts==index}" v-for='(i,index) in typelist' :key="index" @click="navacts=index">
                                    <span>{{i}}</span>
                                </li>
                            </ul>
                        </div>
                        <div class="nav-list">
                            <ul>
                                <li :class="{'list-active':listactive==i}" v-for="(i,index) in list" :key='index' @click="listactive=i">
                                    <div class="nav-list-li">
                                        <!-- <img src="../../../static/imgs/BTC-icon.png"> -->
                                        <h6>{{i.name}}</h6>
                                        <p>{{i.price?(+i.price).toFixed(2):'--'}} ≈{{i.symbolCny?(i.symbolCny).toFixed(2):'--'}} CNY</p>
                                        <p>成交量:{{i.totalVol?(+i.totalVol).toFixed(2):'--'}} </p>
                                    </div>
                                </li>
                            </ul>
                        </div>
                        <em class="border-lefts"></em>
                        <em class="border-rights"></em>
                        <em class="border-bottoms"></em>
                        <em class="border-tops"></em>
                    </div>
                </div>
            </div>
            <!-- 中间 -->
            <div id="chart" :style="{'margin-left': chartleft, 'margin-right': chartright}">
                <div class="chart-num"><img src="../../../static/imgs/left_hide.png" @click="hideleft"> <img @click="hideright" src="../../../static/imgs/right_hide.png">
                    <span>{{listactive.name||'---/---'}}</span>
                    <span :class="market.diffRate>0?'hong':'lv'">{{market.price||'----'}}
                        <!-- <i class="iconfont icon-jiantou-copy-copy"></i> -->
                    </span>
                    <span>涨幅:
                        <em :class="market.diffRate>0?'hong':'lv'">{{market.diffRate||'----'}}%</em>
                    </span>
                    <span>开盘价:
                        <em>{{market.open||'----'}}</em>
                    </span>
                    <span>高:
                        <em>{{market.high||'----'}}</em>
                    </span>
                    <span>低:
                        <em>{{market.low||'----'}}</em>
                    </span>
                </div>

                <div class="chart">
                    <div class="chart-cont">
                        <div class="chart-cont-can">
                            <div id="chart_container" class="f-fill">
                                <!-- tradingview -->
                                <trading-view height='586px' :code='listactive.marketCode' @getPrice='getPrice' :market='market'></trading-view>
                            </div>

                        </div>
                        <em class="border-lefts"></em>
                        <em class="border-rights"></em>
                        <em class="border-bottoms"></em>
                        <em class="border-tops"></em>
                    </div>
                </div>
            </div>
            <!-- 右边 -->
            <div id="news" v-show="!hr">
                <div class="news-top">
                    <span>最新价</span>
                </div>
                <div class="news-list">
                    <div class="news-list-cont">
                        <ul class="clearfix">
                            <li>价格({{coin}})</li>
                            <li>时间</li>
                        </ul>
                        <div class="news-dl" v-for="(i,index) in  newPrice" :key="index" v-if="index<22">
                            <span class="hong">{{i.price}}</span>
                            <em>{{i.time}}</em>
                        </div>
                        <em class="border-lefts"></em>
                        <em class="border-rights"></em>
                        <em class="border-bottoms"></em>
                        <em class="border-tops"></em>
                    </div>
                </div>
            </div>
        </div>
        <div class="app-bottom clearfix">
            <div id="business" class="clearfix">
                <div class="business">
                    <div class="business-cont">
                        <div class="bus-top">
                            <dl class="clearfix">
                                <dt>        
                                    <div class="price-box">
                                        <span>买入价</span> 
                                        <label growing-ignore="true" class="">
                                            <input name="price" autocomplete="off" maxlength="14" class="" v-if="params.type==1" v-model="params.price">
                                            <input maxlength="14" placeholder="以市场上最优价格买入" disabled v-else>
                                            <span style="color: #0079df;" @click="params.type=1^params.type">{{params.type==0?'限价':'市价'}}</span> 
                                            <!-- <div class="estimate-box upper show">≈ 26502.94 cny</div> -->
                                        </label>
                                    </div>
                                    <div class="price-box">
                                        <span>买入量</span> 
                                        <label growing-ignore="true" class="">
                                            <input name="price" autocomplete="off" maxlength="14" class="" v-model="params.volume" @blur="limit">
                                            <span class="currency upper">{{listactive.exchangeCode}}</span> 
                                            <!-- <div class="estimate-box upper show">≈ 26502.94 cny</div> -->
                                        </label>
                                    </div>
                                    <div class="t_base">
                                        <ul>
                                            <li :class="params.lever==i?'active':''"  v-for="i in base" :key="i" @click="params.lever=i">{{i}}倍</li>
                                        </ul>
                                        <span class="risk">风险率 : {{riskRate*100}}%</span>
                                    </div>
                                    <div class="t_money">
                                        <span>可用: {{money?(+money).toFixed(2):'--'}} {{listactive.unitsCode}}</span><span>保证金: {{earnest_money}} {{listactive.unitsCode||'--'}}</span>
                                    </div>

                                </dt>
                                <dd>
                                    <!-- <div class="bus-num">
                                        <span @click="orderlist.hands--">-</span>
                                        <input min="0" v-model="orderlist.hands">
                                        <span @click="orderlist.hands++">+</span>
                                    </div> -->
                                    <div class="_stop">
                                        <div class="s_profit">
                                            <h5 class="s_title">止盈价格:</h5>
                                            <input type="number" placeholder="请输入止盈价格" v-model="params.profitPoints">
                                        </div>
                                        <div class="s_loss">
                                            <h5 class="s_title">止损价格:</h5>
                                            <input type="number" placeholder="请输入止损价格" v-model="params.lossPoints">
                                        </div>
                                    </div>                                    
                                </dd>
                            </dl>
                        </div>
                        <div class="bus-down">
                            <!-- <div class="tab-list">
                                <span>止盈</span>
                                <ul class="clearfix">
                                    <li :class="{'damage':orderlist.profitPoints===i.content}" @click="orderlist.profitPoints=i.content" v-for="(i,index) in StopProfit" :key="index">{{i.title}}</li>
                                </ul>
                            </div>
                            <div class="tab-list">
                                <span>止损</span>
                                <ul class="clearfix">
                                    <li :class="{'surplus':orderlist.lossPoints===i.content}" @click="orderlist.lossPoints=i.content" v-for="(i,index) in TrailingStop" :key="index">{{i.title}}</li>
                                </ul>
                            </div> -->
                        </div>
                        <div class="bus-but">
                            <input type="button" value="市价做多" class="damage" @click="params.direction=0;buy()">
                            <input type="button" value="市价做空" class="surplus" @click="params.direction=1;buy()">
                        </div>
                        <em class="border-lefts"></em>
                        <em class="border-rights"></em>
                        <em class="border-bottoms"></em>
                        <em class="border-tops"></em>
                    </div>
                </div>

            </div>
            <div id="warehouse">
                <div class="warehouse">
                    <div class="warehouse-cont">
                        <div class="war-tab">
                            <span @click="waractive=index" :class="{'war-active':waractive==index}" v-for="(i,index) in warlist" :key="index">{{i}}</span>
                            <div class="war-whole clearfix">
                                <span>总保证金：{{totalprofit.amount?(+totalprofit.amount).toFixed(4):'--'}}</span>
                                <span>{{waractive==0?'浮动':'总'}}盈亏：
                                    <i :class="totalprofit.profit>0?'hong':'lv'">{{totalprofit.profit}}</i>
                                </span>
                                <span>总手续费：{{totalprofit.fees?(+totalprofit.fees).toFixed(4):'--'}}{{coin}}</span>
                                <em v-if="waractive==0">
                                    <i @click="sellall">全部平仓</i>
                                </em>
                            </div>
                        </div>
                        <div class="war-table" >
                            <table border="0" cellspacing="0" cellpadding="0">
                                <!-- <tr>
                                    <th>合约</th>
                                    <th>数量</th>
                                    <th>订单编号</th>
                                    <th>建仓时间</th>
                                    <th>保证金({{coin}})</th>
                                    <th>开仓价</th>
                                    <th>止盈价</th>
                                    <th>止损价</th>
                                    <th>盈亏({{coin}})</th>
                                    <th>操作</th>
                                </tr> -->

                            </table>
                            <div class="war-td" v-if="waractive==0&&userinfo">
                                <table border="0" cellspacing="0" cellpadding="0">
                                    <tr>
                                        <th>合约</th>
                                        <th>数量</th>
                                        <!-- <th>订单编号</th> -->
                                        <th>建仓时间</th>
                                        <th>下单方向</th>
                                        <th>保证金({{coin}})</th>
                                        <th>买入价</th>
                                        <th>止盈价</th>
                                        <th>止损价</th>
                                        <th>盈亏({{coin}})</th>
                                        <th>手续费</th>
                                        <th>操作</th>
                                    </tr>

                                    <tr v-for="(i,index) in tradelist" :key="index">
                                        <td>{{i.symbol}}</td>
                                        <td>{{i.number }}</td>
                                        <!-- <td>{{i.serialNo}}</td> -->
                                        <td>{{i.buyTime}}</td>
                                        <td>{{i.direction==0?'做多':'做空'}}</td>
                                        <td>{{i.orderPrice}}</td>
                                        <td>{{i.buyPoint }}</td>
                                        <td>{{i.profitPoints||'--'}}</td>
                                        <td>{{i.lossPoints||'--' }}</td>
                                        <td  :class="i.fdyk<0?'down':i.fdyk>0?'up':''">{{i.fdyk||'--'}}</td>
                                        <td>{{i.sellFee?(+i.sellFee).toFixed(4):'--'}}{{coin}}</td>
                                        <td><button class="sellbtn" @click="palmodel=true;tradeitem=i">修改</button> <button class="sellbtn" @click="sell(i.id)">平仓</button></td>
                                    </tr>
                                </table>
                                <Page :total="trade.total" size="small" :page-size='trade.rows' @on-change='pagechange' v-if="trade.total>trade.rows" />
                            </div>
                            <div class="war-td" v-if="waractive==1&&userinfo">
                                <table border="0" cellspacing="0" cellpadding="0">
                                    <tr>
                                        <th>合约</th>
                                        <th>数量</th>
                                        <!-- <th>订单编号</th> -->
                                        <th>建仓时间</th>
                                        <th>下单方向</th>
                                        <th>保证金({{coin}})</th>
                                        <th>卖出时间</th>
                                        <th>买入价</th>
                                        <th>卖出价</th>
                                        <th>盈亏({{coin}})</th>
                                        <th>手续费</th>
                                        <!-- <th>操作</th> -->
                                    </tr>
                                    <tr v-for="(i,index) in tradelist" :key="index">
                                        <td>{{i.symbol}}</td>
                                        <td>{{i.number }}</td>
                                        <!-- <td>{{i.serialNo}}</td> -->
                                        <td>{{i.buyTime}}</td>
                                        <td>{{i.direction==0?'做多':'做空'}}</td>
                                        <td>{{i.orderPrice}}</td>
                                        <td>{{i.sellTime}}</td>
                                        <td>{{i.buyPoint }}</td>
                                        <td>{{i.sellPoint }}</td>
                                        <td  :class="i.difMoney<0?'down':i.difMoney>0?'up':''">{{i.difMoney?(+i.difMoney).toFixed(2):'--'}}</td>
                                        <td>{{i.sellFee?(+i.sellFee).toFixed(4):'--'}}{{coin}}</td>
                                        <!-- <td>平仓</td> -->
                                    </tr>
                                </table>
                                <Page :total="trade.total" size="small" :page-size='trade.rows' @on-change='pagechange' v-if="trade.total>trade.rows" />
                            </div>
                            <div class="war-td" v-if="waractive==2&&userinfo">
                                <table border="0" cellspacing="0" cellpadding="0">
                                    <tr>
                                        <th>合约</th>
                                        <th>数量</th>
                                        <!-- <th>订单编号</th> -->
                                        <th>建仓时间</th>
                                        <th>下单方向</th>
                                        <th>保证金({{coin}})</th>
                                        <th>卖出时间</th>
                                        <th>买入价</th>
                                        <th>卖出价</th>
                                        <th>盈亏({{coin}})</th>
                                        <th>手续费</th>
                                        <th>操作</th>
                                    </tr>
                                    <tr v-for="(i,index) in tradelist" :key="index">
                                        <td>{{i.symbol}}</td>
                                        <td>{{i.number }}</td>
                                        <!-- <td>{{i.serialNo}}</td> -->
                                        <td>{{i.buyTime}}</td>
                                        <td>{{i.direction==0?'做多':'做空'}}</td>
                                        <td>{{i.orderPrice}}</td>
                                        <td>{{i.sellTime}}</td>
                                        <td>{{i.buyPoint }}</td>
                                        <td>{{i.sellPoint }}</td>
                                        <td  :class="i.difMoney<0?'down':i.difMoney>0?'up':''">{{i.difMoney}}</td>
                                        <td>{{i.sellFee}}{{coin}}</td>
                                        <td><button class="sellbtn" @click='recall(i.id)'>撤单</button></td>  
                                    </tr>
                                    
                                </table>
                                <Page :total="trade.total" size="small" :page-size='trade.rows' @on-change='pagechange' v-if="trade.total>trade.rows" />
                            </div>
                            <div class="war-sign" v-if="!userinfo">
                                <span>您还没有登录，请
                                    <router-link to="/login">登录</router-link> 或
                                    <router-link to="/register">注册</router-link> 后再尝试</span>
                            </div>
                        </div>
                        <em class="border-lefts"></em>
                        <em class="border-rights"></em>
                        <em class="border-bottoms"></em>
                        <em class="border-tops"></em>
                    </div>
                </div>
            </div>
        </div>
        <Modal
            v-model="confirmshow"
            title="交易风险提示"
            @on-ok="order"
            class-name="vertical-center-modal"
            >
            <p>1、由于合约交易的点差机制，任何交易品种都会有三种价格，分别为“市价"“做多价”“做空价”，买空价与做空价分别是距市价加或减市价的万分之十二的距离，当用户对交易品种进行做多时以”做多价“为成本价进行成交，此时”做空价“即为您的平仓结算价，包含您设置止损止盈时也请以”做空价“为准；当用户对交易品种进行做空时以”做空价“为成本价成交，此时”做多价“即为您的平仓结算价，包含您设置止损止盈时也请以”做多价“为准</p>
            <p>2、当您的账户风险率达到或低于70%时，为避免您的账户因行情原因造成更大的损失，系统将会强制平仓您的所有持有订单，账户风险率计算方式为：账户净值/占用USDT*100%</p>
            <p>3、数字资产合约交易具有杠杆存在，交易损失风险可能性很大，有可能导致您损失账户内部分或全部USDT，因此您应该根据您的财务状况仔细考虑数字资产合约杠杆交易是否适合您</p>
            <br>
            <p>是否确认<b style="font-weight: bolder;">{{orderlist.direction==0?'做多':'做空'}}{{listactive.name}}*{{orderlist.hands}}</b>?</p>
        </Modal>
        <Modal
            v-model="palmodel"
            title="设置止盈止损"
            @on-ok="setpal"
            class-name="vertical-center-modal">
                <div class="bus-down">
                    <div class="tab-list">
                        <span>止盈</span>
                        <Input v-model="pal.profitPoints" placeholder="输入止盈" style="width: 300px" />
                    </div>
                    <div class="tab-list">
                        <span>止损</span>
                        <Input v-model="pal.lossPoints" placeholder="输入止损" style="width: 300px" />
                    </div>
                </div>
        </Modal>
    </div>
</template>

<script type="">
import tradeheader from "../common/tradeheader";
import tradingView from "../tradingView/index";

export default {
    name: "contract",
    data() {
        return {
            userinfo:this.$cookie.get('_auth'),
            typelist: [], //合约类型列表
            navacts: 0, //navbar 选择
            list: [], //合约列表
            listactive: {}, //当前选择的合约
            markettimer: null, //行情定时器
            marketall:[],//全部行情
            market: {}, //行情
            money: 0, //usdt余额
            totalprofit: {
                //总盈亏
                amount: 0,//保证金
                profit: 0,//手续费
                fees:0,//手续费
            },
            StopProfit: [], //止盈
            TrailingStop: [], //止损列表
            orderlist: {
                //下单列表
                contractId: "", //合约ID
                direction: "", //方向 0:买涨 1:买跌
                hands: 0, //数量
                lossPoints: "", //止损点(如 50% 为0.5)
                profitPoints: "" //止盈点(如 50% 为0.5)
            },
            chartleft: "244px",
            chartright: "180px",
            hl: false, //隐藏左边
            hr: false, //隐藏右边
            tradelist: [], //订单列表
            trade:{//订单页数总数
                rows:10,
                start:0,
                total:0
            },
            waractive: 0, //当日持仓选择
            warlist: ["当日持仓", "历史明细", "委托订单"], //当日持仓选项
            confirmshow:false,///下单确认框
            newPrice:[],//最新价
            palmodel:false,//设置止盈止损model
            tradeitem:{},//当前设置止盈止损的那条数据
            pal:{//止盈止损参数
                tradeId:'',
                lossPoints:null,
                profitPoints:null
            },
            params:{//下单参数
                contractId:null,//合约ID
                direction:0,//方向 0:买涨 1:买跌
                type:0,//类型：0-市价，1-限价
                price:null,//购买价格
                volume:null,//购买量
                lever:50,//杠杆比例（如20倍为20）
                lossPoints:null,//止损值
                profitPoints:null,//止盈值
            },
            riskRate:'',//风险率
            base:[],//杠杆倍数
        };
    },
    components: {
        tradeheader,tradingView
    },
    created() {
        this.gettype();
        this.getmoney();
        // this.gettotalprofit();
        this.gettradelist();
        this.get_riskRate()
        this.getlever()
    },
    beforeDestroy() {
        clearInterval(this.markettimer);
        // clearInterval(this.fstimer);
        this.markettimer = null;
        // this.fstimer=null;
    },
    computed: {
        //保证金
        earnest_money(){
            let price
            this.params.type==0?price=this.listactive.price:price=this.params.price        
            let m=price*this.params.volume*(1/this.params.lever)
            return m?(m).toFixed(2):0
        },
        //当前合约货币
        coin(){
            if (this.list.length) {
                return this.listactive.unitsCode
            }
           return '--'
        },
    },
    watch: {
        coin(){
            this.getmoney()
        },
        navacts() {
            //nav切换时重新请求列表
            this.getlist();
            this.setmarkettimer();
            // this.selectMarket()
        },
        // 数量
        "orderlist.hands"(v) {
            if (v < 0) {
                this.orderlist.hands = 0;
            }
            if (+v > +this.listactive.sumHandsLimit) {
                this.orderlist.hands = +this.listactive.sumHandsLimit;
            }
        },
        waractive(){//当日持仓当日订单
            this.gettradelist();
        },
        'listactive.exchangeCode'(){
            this.params.price=this.listactive.price
      },

    },
    methods: {
        //获取倍数
        async getlever(){
            let data=await this.$axios.get('api/trade/contract/leverage')
            this.base=data.data.data.split(',')
            this.params.lever=this.base[0]
        },
        //隐藏侧边栏
        hideleft() {
            if (this.hl == true) {
                this.chartleft = "244px";
                this.hl = false;
                return;
            }
            this.chartleft = "10px";
            this.hl = true;
        },
        hideright() {
            if (this.hr == true) {
                this.chartright = "180px";
                this.hr = false;
                return;
            }
            this.chartright = "10px";
            this.hr = true;
        },
        //获取合约类型
        gettype() {
          return  this.$axios
                .post("api/trade/contract/query")
                .then(res => {
                    if (!res.data.errorCode) {
                        this.list = res.data.data;
                        this.listactive = res.data.data[0];
                        this.setmarkettimer();

                    }
                })
                .catch(err => {});
        },
        //获取合约列表
        getlist() {
            this.$axios
                .post(
                    `api/trade/contract/list_query?contractType=${
                        this.typelist[this.navacts]
                    }`
                )
                .then(res => {
                    if (!res.data.errorCode) {
                        this.list = res.data.data;
                        this.listactive = res.data.data[0];
                        // console.log(this.listactive);
                        // this.setfstimer();
                        this.setmarkettimer();
                        this.getStopProfit();
                        this.getTrailingStop();
                    }
                })
                .catch(err => {});
        },
        //行情定时器
        setmarkettimer() {
            if (this.markettimer) {
                clearInterval(this.markettimer);
                this.markettimer = null;
                this.getmarket();
            } else {
                this.getmarket();
            }
            this.markettimer = setInterval(() => {
                this.getmarket();
                // this.gettotalprofit();
            }, 1000);
        },
        //获取行情
        getmarket() {
            if (!this.list.length) {
                return
            }
            let arr=[]
            this.list.map(i=>{
                arr.push(i.marketCode)
            })
            let marketCode=[...new Set(arr)].join(',')
            this.$axios.get(`api/market/latest_market?codes=${marketCode}`).then(res => {
                    if (!res.data.errorCode) {
                        this.marketall = res.data.data;
                        this.selectMarket(); //选择行情
                        this.setprice()
                        this.marketall.map(v=>{
                             this.list.map((i,index)=>{
                                if (v.code==i.marketCode) {
                                    Object.assign(i, v)
                                    // this.list[index]=Object.assign({},i, v)
                                    // this.symbolList[index] = Object.assign({}, i, v)
                                }
                                if (v.code==this.listactive.marketCode){
                                    this.listactive = Object.assign({}, this.listactive, v)
                                }
                            }) 
                            if (!this.tradelist.length) {
                                return 
                            }
                            this.tradelist.map(i=>{
                                if (i.marketCode==v.code) {
                                    Object.assign(i, v)
                                }
                            })                            
                        })
                        //下单的价格等于当前现价
                        if (!this.params.price) {
                            this.params.price=this.listactive.price
                        }          
                        // console.log(this.market);
                    }
                }).catch(err => {

                });
                
        },
        //获取当前行情
        selectMarket() {
            this.marketall.forEach((i, index) => {
                if (i.code == this.listactive.marketCode) {
                    this.market = i;
                }
            });
        },
        // 设置最新价格 浮动盈亏
        setprice(){
            // totalprofit: {
            //     //总盈亏
            //     amount: 0,//保证金
            //     profit: 0,//手续费
            //     fees:0,//手续费
            // },
            var totalprofit={
                amount: 0,//保证金
                profit: 0,//手续费
                fees:0,//手续费
            }
            if(this.waractive==0){
               var profit='fdyk'
            }else{
                var profit='difMoney'
            }
            this.tradelist.forEach((item,index)=>{
                this.marketall.forEach((i) => {
                    if (item.marketCode==i.code) {
                        this.$set(item,'price',i.price)
                    }
                    if(item.direction== '0'){//当前价 - 建仓价 （买涨）
                        this.$set(item , 'fdyk', ((item.price-item.buyPoint)*item.number).toFixed(2));
                    }else if (item.direction == '1'){//建仓价 - 当前价 （买跌）
                        this.$set(item , 'fdyk', ((item.buyPoint-item.price)*item.number).toFixed(2)   );
                    }
                })
                totalprofit.fees+=item.sellFee
                totalprofit.amount+=item.orderPrice
                if (item[profit]) {
                    totalprofit.profit+=(+item[profit])
                }
                // console.log(+item[profit],profit);
                
                
            })

            totalprofit.profit=totalprofit.profit.toFixed(4)
            Object.assign(this.totalprofit,totalprofit)

        },
              //查询风险值
        get_riskRate(){
            if(!this.$cookie.get('_auth')){
                return 
            }                    
            this.$axios.post('api/trade/contract/riskRate').then(res=>{
                if (!res.data.errorCode) {
                    this.riskRate=res.data.data
                }
            })
        },
        //浮动盈亏
        float(i){
            if (i.direction==0) {
                if ((i.price-i.buyPoint)*i.number) {
                    return ((i.price-i.buyPoint)*i.number).toFixed(2)
                }
                return '--'
            }
            if ((i.buyPoint-i.price)*i.number) {
                return ((i.buyPoint-i.price)*i.number).toFixed(2)                    
            }
            return '--'
        },         
        //获取账户余额
        getmoney() {
            if (this.$cookie.get("_auth")) {
                this.$axios.post(`api/assets/query?type=2`).then(res => {
                    if (!res.data.errorCode) {
                        console.log(res);
                        res.data.data.forEach(item => {
                            console.log(item);
                            if (item.currencyCode == this.coin) {
                                this.money = item.amount;
                            }
                        });
                    }
                });
            }
        },

        //下单确认框
        confirm(direction){
            if (!this.orderlist.hands) {
                this.$Message.warning("委托数量错误");
                return;
            }
            this.orderlist.direction = direction;
            this.orderlist.contractId = this.listactive.id;
            this.confirmshow=true
        },
        //下单
        order() {
            
            console.log(this.orderlist);

            this.$axios({
                url: "api/trade/contract/place_order",
                method: "post",
                data: this.orderlist
            }).then(res => {
                if (!res.data.errorCode) {
                    this.$Message.success("下单成功!");
                    // this.gettotalprofit();
                    this.gettradelist();
                }
            });
        },
        //全部平仓
        sellall(){
            const arr=[]
            this.tradelist.forEach((item,index)=>{
                arr.push(item.id)
            })
            console.log(arr);
            this.sell(arr.join(','))
        },
        //平仓
        sell(id){
            this.$axios.post(`api/trade/contract/do_sell?tradeId=${id}`).then(res=>{
                if (!res.data.errorCode) {
                    this.$Message.success("平仓成功!");
                    // this.gettotalprofit();
                    this.gettradelist();
                }
            }).catch(err=>{

            })
        },
        //撤单
        recall(id){
            this.$axios.post(`api/trade/contract/revoke?tradeIds=${id}`).then(res=>{
              if (!res.data.errorCode) {
                  this.$Message.success('撤回成功')
                    this.gettradelist();
              }
          })
        },
        //获取订单列表
        gettradelist() {
            if (!this.$cookie.get("_auth")) {
                return;
            }
            this.$axios
                .post("api/trade/contract/trade_query", {
                    status: this.waractive==2?3:this.waractive,
                    start: this.trade.start,
                    rows: this.trade.rows
                })
                .then(res => {
                    if (!res.data.errorCode) {
                        this.tradelist = res.data.page.data;
                        this.trade.total=res.data.page.total
                        // console.warn(this.tradelist);
                        
                        // this.getmarket();
                        console.log(this.tradelist);
                    }
            });
        },
        //翻页
        pagechange(index){
            console.log(index);
            this.tradelist=[]
            this.trade.start=(index-1)*this.trade.rows
            this.gettradelist()
            
        },
        // 获取最新价格
        getPrice(data){
            const arr=[]
            data.map(val=>{
                arr.push({
                    price:+val.close,
                    time:val.time.split(' ')[1]
                })
            })
            this.newPrice=arr.reverse()
            console.log('getPrice',data);
            
            // console.log('getPrice',arr.reverse());
        },
        //设置止盈止损
        setpal(){
            this.palmodel=false
            this.pal.tradeId=this.tradeitem.id
            this.$axios.post('api/trade/contract/setUpStop',this.pal).then(res=>{
                if (!res.data.errorCode){
                    this.$Message.success("设置成功!");
                    this.gettradelist();
                }
            })
        },
        //限额      
      limit(){
          if (this.params.volume>this.listactive.maxVolume) {
              this.$Message.warning(`最大限额为 ${this.listactive.maxVolume} ${this.listactive.exchangeCode}`)
              this.params.volume=this.listactive.maxVolume
          }
          if (this.params.volume<0) {
              this.$Message.warning(`最小限额为 ${this.listactive.minVolume} ${this.listactive.exchangeCode}`)
              this.params.volume=this.listactive.minVolume
          }
      },
        //下单
      buy(){
          if (!this.params.volume) {
              this.$Message.info('请输入购买数量')
              return
          }
           this.$Modal.confirm({
                title: '确认下单',
                content: `确认购买${this.listactive.exchangeCode}*${this.params.volume}`,
                onOk: () => {
                    this.params.contractId=this.listactive.id
                    this.$axios.post('api/trade/contract/place_order',this.params).then(res=>{
                        if (!res.data.errorCode) {
                            this.getmoney()
                            this.gettradelist();
                            this.$Message.success('下单成功!')
                        }
                    })
                },
                onCancel: () => {
                    
                }
            });
      },
    }
};
</script>
 
<style scoped lang="less">
    .t_base{
        display: flex;
        justify-content: space-between;
        margin: .7rem 0;
        align-items: center;
        font-size: 12px;
        >ul{
            display: flex;
            justify-content: space-between;
            flex-wrap: wrap; 
            flex:0 0 70%;
            li{
                padding: 5px 10px;
                font-size: 12px;
                border:1px solid #383f66;
                color: #61688a;
                border-radius: 2px;
                margin-bottom: 5px;
                &.active{
                    border-color: rgb(0, 121, 223);
                    color: rgb(0, 121, 223);
                }
            }
        }
        .risk{
            color: #9c9e9c;
        }
    }
    .t_money{
        display: flex;
        justify-content: space-between;
        color: #888f94;
    }
        ._stop{
        display: flex;
        justify-content: space-between;
        margin-bottom: 1rem;
        margin-left: 10px;
        padding-top: 30px;
        width: 100%;
        >div{
            flex:0 0 45%;
        }
        .s_title{
            font-size: 14px;
            margin-bottom: 2rem;
            color: #61688a;
        }
        input{
            font-size: 12px;
            border-bottom: 1px solid #383f66;
            padding-bottom: .2rem;
            background-color: rgba(19, 22, 37, 0.2);
             color: #d2d6ec;   
            outline: 0;
            &::-webkit-input-placeholder {
             color: #d2d6ec;   
            }
        }
    }
.price-box {
    display: flex;
    justify-content: space-between;
    margin-top: 8px;
    position: relative;
    >span {
        position: static;
        font-size: 12px;
        line-height: 30px;
        min-width: 24px;
        margin-right: 8px;
        max-width: 100px;
        white-space: nowrap;
        top: 9px;
        left: 16px;
        color: #61688a;
    }
    label {
        display: inline-block;
        height: 30px;
        line-height: 30px;
        flex: auto;
        width: auto;
        border: 0;
        position: relative;
        padding: 0;
        input{
            font-size: 14px;
            position: static;
            width: 100%;
            border: 1px solid;
            text-indent: 8px;
            height: 30px;
            line-height: 1;
            padding: 10px 0;
            -webkit-box-sizing: border-box;
            box-sizing: border-box;
            color: #d2d6ec;
            border-color: #383f66;
            background-color: rgba(19,22,37,.2);
            &:disabled{
                border:0;
            }
        }
        span {
            margin-top: 8px;
            font-size: 12px;
            height: 18px;
            display: block;
            position: absolute;
            right: 8px;
            top: -7px;
            color: #61688a;                
        }
    }
}

.up{
    color: #c31e36;
}
.down{
    color: #00802b;
}
.damage1 {
    background: #c31e36 !important;
    color: #fff !important;
}
.surplus1 {
    background: #00802b !important;
    color: #fff !important;
}

* {
    box-sizing: content-box;
}
.vertical-center-modal{
        display: flex;
        align-items: center;
        justify-content: center;
        
        .ivu-modal{
            top: 0;
        }
}

.app {
    height: 100%;
    min-width: 1300px;
    background-color: #000;
    color: #fff;
}

.border-lefts,
.border-rights,
.border-bottoms,
.border-tops {
    display: inline-block;
    width: 8px;
    height: 8px;
    position: absolute;
}
.border-lefts {
    border-bottom: 2px solid #188abe;
    border-left: 2px solid #188abe;
    bottom: -1px;
    left: -1px;
}
.border-rights {
    border-top: 2px solid #188abe;
    border-right: 2px solid #188abe;
    right: -1px;
    top: -1px;
}
.border-bottoms {
    border-bottom: 2px solid #188abe;
    border-right: 2px solid #188abe;
    right: -1px;
    bottom: -1px;
}
.border-tops {
    border-top: 2px solid #188abe;
    border-left: 2px solid #188abe;
    top: -1px;
    left: -1px;
}
.war-sign{
    text-align: center;
    line-height: 180px;
    font-size: 12px;
    >span{
    padding-left: 36px;
    height: 26px;
    line-height: 26px;
    background: url(../../../static/imgs/smile.png) no-repeat 0 0;
    background-size: 26px 26px;
    display: inline-block;
    >a{
    color: #1daaec;
}
}
}
.sellbtn{
    border: 1px solid #0079df;
    border-radius: 4px;
    color: #0079df;
    font-size: 12px;
    /* width: 65px; */
    height: 18px;
    line-height: 18px;
    display: inline-block;
    text-align: center;
    cursor: pointer;
    background: #000;
    padding: 0px 5px;
    background: #000;
}
.app-in {
    position: relative;
    // 左边
    #Tab {
        padding-left: 14px;
        width: 236px;
        box-sizing: border-box;
        position: absolute;
        top: 0;
        left: 0;
        .money,
        .tab-nav {
            width: 100%;
            border: 1px solid #0a3b4c;
            margin-top: 4px;
            padding-bottom: 4px;
            padding-right: 4px;
            box-sizing: border-box;
            margin-bottom: 20px;
        }
        .money {
            .money-cont {
                border: 1px solid #0a3b4c;
                margin-top: -4px;
                margin-left: -4px;
                background: #000;
                padding: 14px 0 14px 25px;
                position: relative;
                > p {
                    line-height: 25px;
                    font-size: 14px;
                }
            }
        }
        .tab-nav {
            .nav-cont {
                border: 1px solid #0a3b4c;
                margin-top: -4px;
                margin-left: -4px;
                background: #000;
                position: relative;
                padding-bottom: 20px;
                .nav-head {
                    padding: 15px 16px;
                    li {
                        float: left;
                        width: 50%;
                        text-align: center;
                        font-size: 13px;
                        height: 22px;
                        line-height: 22px;
                        background: #01080b;
                        background: linear-gradient(
                            180deg,
                            #01080b 0,
                            #082d3e 28%,
                            #09374c 50%,
                            #082d3e 80%,
                            #01080b
                        );
                        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#01080b",endColorstr="#01080b",GradientType=0);
                        > span {
                            display: inline-block;
                            background: #000;
                            width: calc(100% - 2px);
                            cursor: pointer;
                        }
                    }
                    .nav-active > span {
                        color: #f4f100;
                    }
                }
                .nav-list {
                    margin: 0 10px;
                    height: 436px;
                    overflow: hidden;
                    overflow-y: scroll;
                    > ul {
                        background: #000;
                        background: linear-gradient(
                            90deg,
                            #000 0,
                            #083248 20%,
                            #1baef3 40%,
                            #1eabf0 53%,
                            #1baef3 67%,
                            #083248 87%,
                            #000
                        );
                        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#000000",endColorstr="#000000",GradientType=1);
                        padding: 1px 0;
                        li {
                            background: #000;
                            padding: 3px 0;
                            margin-bottom: 1px;
                            .nav-list-li {
                                text-align: right;
                                line-height: 26px;
                                padding: 13px 26px;
                                cursor: pointer;
                                position: relative;
                                > img {
                                    position: absolute;
                                    top: 15px;
                                    left: 28px;
                                    z-index: 1;
                                    width: 48px;
                                }
                                > h6 {
                                    font-size: 15px;
                                }
                                > p {
                                    font-size: 12px;
                                }
                            }
                        }
                        .list-active .nav-list-li {
                            background: #000;
                            background: linear-gradient(
                                90deg,
                                #000 0,
                                #030f15 20%,
                                #082c3d 39%,
                                #093246 53%,
                                #082c3d 69%,
                                #030f15 87%,
                                #000
                            );
                            filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#000000",endColorstr="#000000",GradientType=1);
                        }
                    }
                }
            }
        }
    }
    // 中间
    #chart {
        position: relative;
        .chart-num {
            padding: 0 36px 25px;
            height: 28px;
            line-height: 28px;
            font-size: 15px;
            position: relative;
            > img {
                width: 23px;
                position: absolute;
                top: 3px;
                cursor: pointer;
            }
            > img:first-child {
                left: 10px;
            }
            > img:nth-child(2) {
                right: 10px;
            }
            > span {
                display: inline-block;
                text-align: center;
                padding: 0 1.8%;
                > i {
                    font-size: 14px;
                    padding-left: 2px;
                }
                > em {
                    padding-left: 5px;
                }
            }
        }
        .chart {
            border: 1px solid #0a3b4c;
            padding-bottom: 4px;
            padding-right: 4px;
            box-sizing: border-box;
            margin-left: 4px;
            margin-bottom: 20px;
            .chart-cont {
                border: 1px solid #0a3b4c;
                margin-top: -4px;
                margin-left: -4px;
                position: relative;
                height: 586px;
                background: #000;
                .chart-cont-can {
                    width: 100%;
                    height: 100%;
                    background: url(../../../static/imgs/bg.png) no-repeat 50%
                        50%;
                    background-size: 130px 136px;
                    position: relative;
                }
                .f-fill {
                    width: 100%;
                    height: 100%;
                }
            }
        }
    }
    // 右边
    #news {
        width: 170px;
        padding-right: 10px;
        box-sizing: border-box;
        position: absolute;
        top: 0;
        right: 0;
        .news-top {
            font-size: 17px;
            color: #1987da;
            height: 24px;
            > span {
                background: url(../../../static/imgs/computer.png) no-repeat 0 0;
                background-size: 22px;
                display: inline-block;
                padding-left: 36px;
                height: 24px;
            }
        }
        .news-list {
            margin-top: 28px;
            width: 100%;
            border: 1px solid #0a3b4c;
            padding-bottom: 4px;
            padding-right: 4px;
            box-sizing: border-box;
            .news-list-cont {
                border: 1px solid #0a3b4c;
                margin-top: -4px;
                margin-left: -4px;
                background: #000;
                position: relative;
                padding: 10px 12px;
                height: 567px;
                > ul > li {
                    float: left;
                    width: 50%;
                    font-size: 12px;
                    line-height: 25px;
                    text-align: center;
                }
                .news-dl > em,
                .news-dl > span {
                    float: left;
                    width: 50%;
                    font-size: 12px;
                    line-height: 25px;
                    display: inline-block;
                }
                .news-dl > em {
                    text-align: right;
                }
            }
        }
    }
}
.app-bottom {
    // 右边
    #business {
        float: right;
        padding: 0 10px;
        width: 33%;
        box-sizing: border-box;
        .business {
            border: 1px solid #0a3b4c;
            padding-bottom: 4px;
            padding-right: 4px;
            box-sizing: border-box;
            margin-left: 4px;
            margin-bottom: 10px;
            float: left;
            width: calc(100% - 4px);
            .business-cont {
                border: 1px solid #0a3b4c;
                margin-top: -4px;
                margin-left: -4px;
                position: relative;
                background: #000;
                padding: 15px;
                height: 244px;
                box-sizing: border-box;
                .bus-top dt {
                    float: left;
                    width: 50%;
                    font-size: 13px;
                    > p {
                        line-height: 22px;
                        padding-left: 20px;
                    }
                }
                .bus-top dd {
                    float: left;
                    width: 50%;
                    padding: 5px 0;
                    .bus-num {
                        width: 90%;
                        border: 1px solid #0a3b4c;
                        border-radius: 20px;
                        height: 30px;
                        overflow: hidden;
                        position: relative;
                        > span {
                            display: inline-block;
                            width: 40px;
                            cursor: pointer;
                            text-align: center;
                            font-size: 25px;
                            position: absolute;
                            top: 0;
                            left: calc(100% - 40px);
                            height: 30px;
                            line-height: 28px;
                            color: #0a3b4c;
                            border-left: 1px solid #0a3b4c;
                        }
                        > span:first-child {
                            left: 0;
                            border-right: 1px solid #0a3b4c;
                            border-left: 0;
                        }
                        > input {
                            margin: 1px 40px;
                            width: calc(100% - 80px);
                            height: 28px;
                            text-align: center;
                            outline: none;
                            font-size: 14px;
                            color: #fff;
                            background: #000;
                        }
                    }
                }
                // .bus-down {
                //     .tab-list {
                //         padding: 15px 18px 0;
                //         position: relative;
                //         > span {
                //             display: inline-block;
                //             width: 45px;
                //             height: 30px;
                //             line-height: 30px;
                //             font-size: 15px;
                //             color: #005db0;
                //             position: absolute;
                //             top: 15px;
                //             left: 18px;
                //             z-index: 1;
                //         }
                //         > ul {
                //             margin-left: 45px;
                //             border: 2px solid #072a39;
                //             border-radius: 4px;
                //             overflow: hidden;
                //             > li {
                //                 float: left;
                //                 width: 16.666%;
                //                 height: 30px;
                //                 line-height: 30px;
                //                 font-size: 15px;
                //                 color: #fff;
                //                 text-align: center;
                //                 cursor: pointer;
                //                 background: #062432;
                //                 border-right: 2px solid #000203;
                //                 box-sizing: border-box;
                //             }
                //         }
                //     }
                // }
                .bus-but {
                    padding-top: 25px;
                    text-align: center;
                    > input {
                        height: 30px;
                        font-size: 16px;
                        color: #fff;
                        outline: none;
                        border: 0;
                        width: 30%;
                        border-radius: 30px;
                        cursor: pointer;
                        margin-right: 10%;
                    }
                    > input:last-child {
                        margin-right: 0;
                    }
                }
            }
        }
    }
    
    // 左边
    #warehouse {
        float: left;
        width: 67%;
        padding: 0 0 0 10px;
        box-sizing: border-box;
        .warehouse {
            border: 1px solid #0a3b4c;
            padding-bottom: 4px;
            padding-right: 4px;
            box-sizing: border-box;
            margin-left: 4px;
            margin-bottom: 10px;
            .warehouse-cont {
                .war-td{
                    >ul{
                        width: 100%;
                        text-align: center;
                    }
                }
                border: 1px solid #0a3b4c;
                margin-top: -4px;
                margin-left: -4px;
                position: relative;
                background: #000;
                padding: 10px;
                .war-tab {
                    height: 42px;
                    > span {
                        display: inline-block;
                        width: 74px;
                        height: 23px;
                        text-align: center;
                        font-size: 13px;
                        background: #062432;
                        line-height: 23px;
                        border-radius: 4px;
                        margin: 10px 5px 0;
                        cursor: pointer;
                    }
                    .war-active {
                        background: #2b4461 !important;
                    }
                    .war-whole {
                        position: absolute;
                        top: 20px;
                        right: 10px;
                        background: #031016;
                        background: linear-gradient(
                            180deg,
                            #031016 0,
                            #051c28 12%,
                            #083144 30%,
                            #0d4b68 46%,
                            #0d4b68 55%,
                            #083144 70%,
                            #051c28 89%,
                            #031016
                        );
                        filter: progid:DXImageTransform.Microsoft.gradient(startColorstr="#031016",endColorstr="#031016",GradientType=0);
                        > span {
                            float: left;
                            display: inline-block;
                            padding: 0 10px;
                            font-size: 12px;
                            color: #fff;
                            background: #000;
                            height: 20px;
                            line-height: 20px;
                            margin-left: 1px;
                        }
                        > em {
                            float: left;
                            padding: 0 30px;
                            margin-left: 1px;
                            background: #000;
                            > i {
                                border: 1px solid #0079df;
                                border-radius: 4px;
                                color: #0079df;
                                font-size: 12px;
                                width: 65px;
                                height: 18px;
                                line-height: 18px;
                                display: inline-block;
                                text-align: center;
                                cursor: pointer;
                                background: #000;
                            }
                        }
                    }
                }
                .war-table {
                    table {
                        width: 100%;
                        th {
                            height: 30px;
                            background: #062432;
                            font-size: 12px;
                            line-height: 30px;
                            text-align: center;
                        }
                        // th:first-child {
                        //     width: 10%;
                        // }
                        // th:nth-child(2) {
                        //     width: 4%;
                        // }
                        // th:nth-child(2) {
                        //     width: 4%;
                        // }
                        // th:nth-child(2) {
                        //     width: 4%;
                        // }
                        // th:nth-child(3) {
                        //     width: 12%;
                        // }
                        // th:nth-child(4) {
                        //     width: 17%;
                        // }
                        // th:nth-child(5) {
                        //     width: 10%;
                        // }
                        // th:nth-child(6),
                        // th:nth-child(7),
                        // th:nth-child(8) {
                        //     width: 9%;
                        // }
                        // th:nth-child(10) {
                        //     width: 10%;
                        // }
                    }
                    .war-td {
                        /* margin-top: 10px; */
                        height: 180px;
                        overflow: auto;
                        table {
                            width: 100%;
                            td {
                                text-align: center;
                                line-height: 30px;
                            }
                        }
                    }
                }
            }
        }
    }
}
                .bus-down {
                    .tab-list {
                        padding: 15px 18px 0;
                        position: relative;
                        > span {
                            display: inline-block;
                            width: 45px;
                            height: 30px;
                            line-height: 30px;
                            font-size: 15px;
                            color: #005db0;
                            // position: absolute;
                            top: 15px;
                            left: 18px;
                            z-index: 1;
                        }
                        > ul {
                            margin-left: 45px;
                            border: 2px solid #072a39;
                            border-radius: 4px;
                            overflow: hidden;
                            > li {
                                float: left;
                                width: 16.666%;
                                height: 30px;
                                line-height: 30px;
                                font-size: 15px;
                                color: #fff;
                                text-align: center;
                                cursor: pointer;
                                background: #062432;
                                border-right: 2px solid #000203;
                                box-sizing: border-box;
                            }
                        }
                    }
                }
// 历史订单
#historyPop {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 10;
    background: hsla(0, 0%, 56%, 0.6);
    .history-pop {
        position: absolute;
        width: 1000px;
        top: 50%;
        left: 50%;
        margin-left: -500px;
        margin-top: -360px;
        border: 1px solid #0a3b4c;
        box-sizing: border-box;
        padding-bottom: 4px;
        padding-right: 4px;
        .history-cont {
            border: 1px solid #0a3b4c;
            position: relative;
            background: #000;
            margin-top: -4px;
            margin-left: -4px;
            .his-Cel {
                position: absolute;
                top: -25px;
                right: -25px;
                width: 25px;
                height: 25px;
                border-radius: 50%;
                background: #000;
                border: 1px solid #0a3b4c;
                line-height: 25px;
                text-align: center;
                font-size: 20px;
                cursor: pointer;
            }
            .history-top {
                padding: 15px 0;
                dd,
                dt {
                    float: left;
                    width: 50%;
                    box-sizing: border-box;
                    font-size: 12px;
                }
                dt {
                    padding-left: 45px;
                    > p > em {
                        display: inline-block; // width: 32px;
                        position: relative;
                        height: 16px; // text-indent: 27px;
                        > i {
                            position: absolute;
                            top: 0;
                            left: -23px;
                            font-size: 16px;
                        }
                    }
                    > p:last-child {
                        padding-top: 6px;
                        > em {
                            text-indent: 0;
                        }
                    }
                }
                dd {
                    padding-right: 45px;
                    text-align: right;
                    > img {
                        height: 30px;
                    }
                }
            }
            .history-tab {
                height: 34px;
                background: #062432;
                padding: 3px 30px;
                box-sizing: border-box;
                > ul {
                    display: inline-block;
                    border: 1px solid #0a0204;
                    border-radius: 4px;
                    overflow: hidden;
                    > li {
                        float: left;
                        width: 84px;
                        height: 25px;
                        line-height: 25px;
                        text-align: center;
                        font-size: 14px;
                        cursor: pointer;
                        .his-act {
                            background: #2b4461;
                        }
                    }
                }
            }
            .history-list {
                padding: 12px 16px;
                height: 598px;
                overflow: auto;
                position: relative;
                margin-bottom: 34px;
                > table th {
                    font-size: 13px;
                    color: #1986d9;
                    height: 32px;
                    line-height: 32px;
                }
            }
            .list-one table {
                width: 1300px !important;
            }
        }
    }
    #page {
        text-align: center;
        position: absolute;
        left: 0;
        right: 0;
        bottom: 5px;
        z-index: 1;
        .pagination {
            display: inline-block;
            > li {
                float: left;
                width: 20px;
                height: 20px;
                border: 1px solid #999;
                text-align: center;
                font-size: 12px;
                line-height: 20px;
                margin-left: 8px;
                border-radius: 4px;
                a {
                    color: #fff;
                    display: inline-block;
                    width: 100%;
                    height: 100%;
                }
            }
            .active {
                border: 1px solid #35578f !important;
            }
            .disabled a {
                color: #eee !important;
            }
        }
    }
}
</style>
