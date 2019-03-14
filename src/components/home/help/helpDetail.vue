<template>
    <div class="wrap">
      <div class="container">
        <div class="dw-title">
          帮助中心详情
        </div>
        <div class="dw-body">
          <div>
            <h3>{{tit}}</h3>
            <p v-html="context"></p>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
    export default {
      name: "helpDetail",
      data() {
        return {
          tit: '',
          context: '',
        }
      },
      mounted() {
        this.detailsInfo();
      },
      methods: {
        detailsInfo() {
          this.$axios.post('/api/user/msg/details',{msgId: this.$route.query.queryId}).then(res => {
            if(res.data.errorCode == 0) {
              this.tit = res.data.data.title;
              this.context = res.data.data.content
            }
          })
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
  .dw-body > div {
    padding: 38px 58px;
    width: 80%;
    margin: auto;
  }
  .dw-body > div h3 {
    text-align: center;
    padding-bottom: 50px;
  }
  .dw-body > div p {
    line-height: 30px;
  }
</style>
