<template>
  <div>
    <Header
      :title="title"
      :href="href"
      :close="clo"
      @warning="warning"
    />
    <!--考试列表-->
    <div class="container">

      <div class="no-complete" v-if="!maxscore">
        <div class="title">未完成</div>
        <!--列表组件-->
        <ul>
          <li class="flex">
            <div><span>党的十九大精神学习试题</span></div>
            <div class="flex flex-align-center flex-justify-center">
              <button @click="routeLink(user_id)" :data-user_id="user_id" :disabled="examState">考试</button>
            </div>
          </li>
        </ul>
      </div>


      <div class="complete" v-if="maxscore">
        <div class="title">已完成</div>
        <!--列表组件-->
        <ul>
          <li class="flex">
            <div>
              <span>党的十九大精神学习试题</span>
              <p>结业得分：{{maxscore}}</p>
            </div>
            <div class="flex flex-align-center flex-justify-center">
              <button class="back" :data-user_id="user_id" @click="routeLink(user_id)" :disabled="examState">重考</button>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <!--modal-->
    <div class="modal" v-if="result">
      <div class="modal-diag">
        <div class="modal-bg">
          <p>你有一次考试没完成，是否继续?</p>
        </div>
        <div class="btn-group">
          <button @click="continuer">继续</button>
          <button @click="again">重新开始</button>
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
  import {request} from '../../api/common'

  export default {
    name: 'AnswerList',
    components: {
      Header,
      Loading,
    },
    data() {
      return {
        title: '在线考试',
        href: '',
        data: [],//页面渲染的数据
        notTest: [],//没有考试
        alreadyTest: [],//可以考试
        result: false,
        user_id: '',
        user_name: '',
        maxscore: '',
        loading: true,//加载动画
        state: true,
        clo: true,
        version: '',
        size: 2,
        examState: true,
      }
    },
    created: function () {
      this.loading = true;
      this.user_name = this.$route.query.user_name;
      this.user_id = this.$route.query.user_id;
      // this.user_id = 111111;
      console.log('user_name:' + this.user_name)
      console.log('user_id:' + this.user_id)
      console.log(this.user_id)
      console.log(request.getServerData)
      request.getServerData(
        {user_id: this.user_id},
        "onlineExamination.action.examinationAction[getHighestScore]",
        true,
        (result) => {
          console.log(result)
          console.log(result.maxscore)
          console.log(result.maxscore.MAXSCORE)
          console.log(result.responsesnum.RESPONSESNUM)
          this.examState = result.responsesnum.RESPONSESNUM == this.size;
          this.maxscore = result.maxscore.MAXSCORE;
          this.loading = false;
        });

    },
    methods: {
      getSession(user_id) {
        console.log(user_id)
        return JSON.parse(localStorage.getItem([user_id]));
      },
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

      routeLink(e) {
        console.log(e)
        this.user_id = e;
        var state = this.getSession(e)
        console.log('state:' + state)
        if (state) {
          this.result = !this.result;
        }
        else {
          this.continuer();
        }

      },
      continuer() {
        this.$router.push({
          path: 'answer',
          query: {'user_id': this.user_id, 'user_name': this.user_name, 'state': this.state}
        })
      },
      again() {
        localStorage.removeItem([this.user_id]);
        this.$router.push({
          path: 'answer',
          query: {'user_id': this.user_id, 'user_name': this.user_name, 'state': this.state}
        })
      }
    }
  }

</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
  @import '../../variable';

  .container {
    position: absolute;
    top: 88px;
    width: @max;
    .title {
      background-color: @answerList-title-bg-col;
      font-size: @answerList-font-24;
      color: @answerList-col-62;
      padding: 0 25px;
      box-sizing: border-box;
      line-height: 62px;
    }
    ul {
      li {
        border-bottom: 1px solid @answerList-col-dc; /*no*/
        div:not(.flex) {
          width: 590px;
          text-overflow: ellipsis;
          overflow: hidden;
          white-space: nowrap;
          padding: 17px 0 17px 25px;
          box-sizing: border-box;
          span {
            color: @answerList-col-62;
            font-size: @answerList-font-32;
            height: 88px;
            line-height: 48px;
          }
          p {
            font-size: @answerList-font-24;
            color: @theme;
            line-height: 32px;
          }
        }
        div.flex {
          flex-grow: 1;
          button {
            border-radius: 25px; /*no*/
            border: 0;
            color: @white;
            font-size: @answerList-font-24;
            background-color: @theme;
            height: 60px;
            line-height: 60px;
            padding: 0 37px;
            box-sizing: border-box;
          }
          button.back {
            color: @theme;
            background-color: @answerList-col-feecec;
          }
          button:disabled {
            background-color: @answerList-col-a8;
            color: @white;
          }
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
    .modal-diag {
      position: absolute;
      top: 50%;
      left: 50%;
      margin-left: -310px;
      margin-top: -309px;
      .modal-bg {
        width: 620px;
        height: 520px;
        background: url(../../assets/modal-bg.png) no-repeat;
        background-size: cover;
        position: relative;
        p {
          width: 520px;
          color: @theme;
          font-size: 30px;
          position: absolute;
          bottom: 46px;
          left: 50%;
          margin-left: -260px;
          text-align: center;
        }
      }
      .btn-group {
        width: @max;
        height: 98px;
        display: table;
        content: '';
        clear: both;
        button {
          color: @white;
          font-size: 30px;
          background-color: @theme;
          height: 98px;
          line-height: 98px;
          border: 0;
          width: @max/2;
          box-sizing: border-box;
          padding: 0;
          margin: 0;
          float: left;
        }
        button:last-child {
          background-color: #918181;
        }
      }
    }
  }
</style>
