<template>
  <div class="healthStandard">
    <header>
      <i class="fl" @click="goBack()"></i>
      <div class="sign-out fr">国家标准</div>
      <div class="header-center">国家学生体质健康标准</div>
    </header>
    <main>
      <div class="synthesisScore" @click="overlayShow=true">
        <ul>
          <li>
            <span>综合得分</span>
            <!-- <span v-text="scoreList.cervixDate"></span> -->
            <span>
              体测日期
              <i v-text="scoreList.cervixDate"></i>
            </span>
          </li>
          <li>
            <div>
              <span v-text="scoreList.grade"></span>
              <span v-text="scoreList.chinaGrade"></span>
            </div>
            <span>
              距优秀差
              <span v-text="scoreList.differenceGrade"></span>分
            </span>
          </li>
        </ul>
        <p>
          <i>!</i>
          <span>
            上一次综合总得分
            <span v-text="scoreList.lastTimeGrade"></span>分
          </span>
        </p>
      </div>
    </main>
    <div class="healthStandard-bottom">
      <img :src="tcjl" alt v-if="flog==1" />
      <div class="indicator" v-if="flog==0">
        <p>
          <span>单项指标</span>
          <span>日期</span>
          <span>结果</span>
          <span>得分</span>
        </p>
        <van-collapse v-model="activeNames" accordion>
          <van-collapse-item v-for="(item,index) in indicatorList" :key="index" :name="index">
            <!-- 折叠面板标题 -->
            <span slot="title">{{item.singleIndex}}</span>
            <span slot="title">{{item.date}}</span>
            <span slot="title">{{item.result}}</span>
            <span slot="title">{{item.score}}</span>
            <!-- 折叠面板内容 -->
            <div v-for="(vv,ii) in item.itemList" :key="ii">
              <span>{{vv.singleIndex}}</span>
              <span>{{vv.date}}</span>
              <span>{{vv.result}}</span>
              <span>{{vv.score}}</span>
            </div>
          </van-collapse-item>
        </van-collapse>
        <div class="btn flexCenter" @click="singleSelfTestTo()">
          <van-button type="default" round size="large" color="#FF7A18">单项自测</van-button>
        </div>
      </div>
    </div>
    <!-- 遮罩层综合得分弹窗 -->
    <van-overlay :show="overlayShow" @click="show = false">
      <div class="wrapper" @click.stop>
        <div class="block">
          <div class="block-top">
            <h4>综合总得分详情</h4>
            <div
              :class="'scorebox'+index"
              v-for="(item,index) in scoreDetailList"
              :key="index"
              ref="scoreDom"
            >
              <div class="orange-boxs" v-for="(vv,ii) in item" :key="ii">
                <p>{{vv.sportEvents}}</p>
                <p>
                  <span v-text="extraPoint"></span>
                  <span>{{vv.percent}}</span>
                </p>
                <p>
                  <span>{{vv.score}}分</span>
                </p>
                <i>+</i>
              </div>
            </div>
          </div>
          <div class="block-bottom" @click="IKnow()">我知道了</div>
        </div>
      </div>
    </van-overlay>
  </div>
</template>
<script>
import tcjl from "@/assets/gbtcjl.png";
export default {
  data() {
    return {
      tcjl: tcjl,
      overlayShow: false,
      // extraPointShow: 1,
      flog: 0,
      activeNames: "1",
      // 综合得分列表
      // scoreList: {
      //   cervixDate: "暂无体测记录",
      //   grade: "00.0",
      //   chinaGrade: "暂无",
      //   differenceGrade: "0.00",
      //   lastTimeGrade: "0.00"
      // },
      scoreList: {
        cervixDate: "2018-12-18",
        grade: "86.5",
        chinaGrade: "良好",
        differenceGrade: "3.5",
        lastTimeGrade: "82.5"
      },
      // 单项指标列表
      indicatorList: [
        {
          singleIndex: "肺活量",
          date: "2018-12-12",
          result: "2187ml",
          score: "96"
        },
        {
          singleIndex: "50米*8往返跑",
          date: "2018-12-10",
          result: "1,35分",
          score: "86",
          itemList: [
            {
              singleIndex: "",
              date: "2018-10-01",
              result: "1,54分",
              score: "82"
            },
            {
              singleIndex: "",
              date: "2018-08-12",
              result: "1,65分",
              score: "80"
            },
            {
              singleIndex: "",
              date: "2018-07-15",
              result: "1,8分",
              score: "68"
            }
          ]
        },
        {
          singleIndex: "仰卧起坐",
          date: "2018-10-16",
          result: "38个",
          score: "96"
        },
        {
          singleIndex: "跳绳",
          date: "2018-09-14",
          result: "78个",
          score: "43"
        }
      ],
      // 遮罩层得分详情列表
      extraPoint: "占",
      scoreDetailList: [
        {
          sportEvents: "50米跑",
          percent: "20%",
          score: "17.6"
        },
        {
          sportEvents: "50米*8往返跑",
          percent: "40%",
          score: "38.4"
        },
        {
          sportEvents: "1分钟跳绳",
          percent: "10%",
          score: "9.1"
        },
        {
          sportEvents: "1分钟仰卧起坐",
          percent: "20%",
          score: "18.6"
        },
        {
          sportEvents: "坐位体前屈",
          percent: "10%",
          score: "8.9"
        },
        {
          sportEvents: "坐位体前屈",
          percent: "附加分",
          score: "5"
        }
      ]
    };
  },
  created() {
    this.processData();
    this.$nextTick(() => {
      this.addclass();
    });
  },
  methods: {
    singleSelfTestTo() {
      this.$router.push({ name: "singleSelfTest" });
    },
    // 处理scoreDetailList数据
    processData() {
      let result = [];
      for (var i = 0; i < this.scoreDetailList.length; i += 3) {
        result.push(this.scoreDetailList.slice(i, i + 3));
      }
      this.scoreDetailList = result;

      // this.scoreDetailList.map(item => {
      //   item.forEach((vv, ii) => {
      //     if (vv.percent === "附加分") {
      //       console.log(vv, ii);
      //       this.extraPointShow = "extraPointShow";
      //     }
      //   });
      // });
    },
    // 添加类名
    addclass() {
      console.log(this.$refs.scoreDom);
      this.$refs.scoreDom.map(item => {
        console.log(item);

        // item.forEach((vv, ii) => {
        //   console.log(vv, ii);
        // });
      });
    },
    IKnow() {
      this.overlayShow = false;
    }
  }
};
</script>
