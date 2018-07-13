<template>
  <div class="container">
    <Header
      :title="title"
      :href="href"
      :close='clo'
      @warning="warning"
    />
    <div class="result">
      <div class="result-top"></div>
      <div class="result-content">
        <div class="result-score">
          <h1>{{score}}<span>分</span></h1>
          <div class="tips">
            <em></em>
            <span>得分</span>
            <em></em>
          </div>
          <img src="../../assets/pass-x.png" alt="" v-if="pass">
          <img class="no" src="../../assets/no-x.png" alt="" v-if="!pass">
        </div>
        <div class="result-img">
          <img src="../../assets/pass-lg.png" alt="" v-if="pass">
          <img src="../../assets/no-lg.png" alt="" v-if="!pass">
        </div>
        <div class="result-description">
          <p v-if="pass">恭喜你，通过测试!</p>
          <p class="no" v-if="!pass">请再接再厉呀!</p>
        </div>
      </div>

    </div>
    <!--loading-->
    <Loading v-if="loading"/>
  </div>
</template>

<script>
  import Header from '../common/Header';
  import Loading from '../common/Loading';
  import req from '../../api/index';
  import {request} from '../../api/common'

  export default {
    name: 'Answer',
    components: {
      Header,
      Loading,
    },
    data() {
      return {
        title: '考试结果',
        /* message: '',*/
        href: '/',
        score: 0,
        pass: 0,
        loading: true,
        classid: '',
        user_id: '',
        user_name: '',
        examList: [],
        state: false,
        clo: false,
      }
    },
    created: function () {
      console.log(this.$route.query)
      this.result = JSON.parse(this.$route.query.resStr)
      console.log(this.result)
      this.loading = true;
      this.state = this.result.state;
      console.log(this.result.qualifi.length)
      console.log(this.result.score)
      this.pass = this.result.qualifi.length == 2 ? 1 : 0;
      this.score = this.result.score;
      var data = this.getSession(this.user_id);
      console.log(data)
      this.examList = data;
      this.loading = false;
      // this.getExamInfo();
    },
    methods: {
      warning(data) {
        console.log(data);
        this.version = data;
        if (this.version == 'android') {
          _ActionJS.exitCurrentPage();
        }
        else if (this.version == 'ios') {
          exitCurrentPage();
        }
      },
      getExamInfo: function () {
        request.getServerData(
          {
            examinationPaperList: this.examlist,
            user_id: this.user_id,
            user_name: this.user_name,
            /* user_name: "张三",*/
            start_time: "2018-6-1 15:00:00",
            end_time: "2018-6-1 20:00:00"
          },
          "onlineExamination.action.examinationAction[markingPapers]",
          true,
          (result) => {
            console.log(result)
            console.log(result.score)
            this.score = result.score;
            this.loading = false;


          })
      },
      getSession(classid) {
        return JSON.parse(localStorage.getItem([classid]));
      },
    }
  }
</script>

<style scoped lang="less">
  @import '../../variable';

  .container {
    height: @max;
    /*background-color: #f6f6f6;*/
  }

  .result {
    position: relative;
    .result-top {
      background: url(../../assets/result-top.png) no-repeat;
      background-size: cover;
      width: @max;
      height: 400px;
    }
    .result-content {
      padding: 59px;
      box-sizing: border-box;
      width: 660px;
      height: 870px;
      position: absolute;
      top: 36%;
      left: 50%;
      margin-left: -330px;
      background-color: @white;
      border-radius: 5px;
      .result-score {
        color: #ffa800;
        position: relative;
        h1 {
          font-size: 120px;
          text-align: center;
          line-height: 220px;
          span {
            font-size: 24px;
          }
        }
        .tips {
          font-size: 28px;
          color: #757575;
          text-align: center;
          em {
            border-bottom: 1px solid #dbdbdb; /*no*/
            display: inline-block;
            width: 117px;
            vertical-align: middle;
          }
        }
        img {
          position: absolute;
          top: 0;
          right: -50px;
          width: 171px;
          height: 149px;
        }
        img.no {
          width: 153px;
          height: 153px;
        }
      }
      .result-img {
        padding-top: 62px;
        img {
          width: 528px;
          height: 182px;
        }

      }
      .result-description {
        padding-top: 119px;
        p {
          text-align: center;
          color: @theme;
          font-size: 34px;
        }
      }
    }
  }

  .modal {
    position: fixed;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    width: @max;
    height: @max;
    background-color: rgba(0, 0, 0, .4);
    img {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 300px;
      height: 300px;
      margin-left: -150px;
      margin-top: -150px;
    }
  }
</style>
