<template>
  <div v-loading="loading">
    <div class="beat flex around">
      <div class="one flex center">
        <div class="box">
          <p>PE</p>
          <div class="num">{{data.pe}}%</div>
        </div>
      </div>
      <div class="one flex center">
        <div class="box">
          <p>速度开动率</p>
          <div class="num">{{data.pe1}}%</div>
        </div>
      </div>
      <div class="one flex center">
        <div class="box">
          <p>净开动率</p>
          <div class="num">{{data.pe2}}%</div>
        </div>
      </div>
      <div class="two flex center">
        <div class="box">
          <p>标准节拍</p>
          <div class="num">{{data.clock0}}s/件</div>
        </div>
      </div>
      <div class="two flex center">
        <div class="box">
          <p>产量</p>
          <div class="num">{{data.quantity}}</div>
        </div>
      </div>
      <div class="two flex center">
        <div class="box">
          <p>开动时间</p>
          <div class="num">{{data.workTime}}</div>
        </div>
      </div>
    </div>
    <!-- <div class="content flex around align-center">
      <div class="in">
        产线节拍异常点个数：
        <span style="color:red">{{data.alertList.length}}</span>
      </div>
      <div class="in">
        最大节拍：
        <span>{{data.clockMax}}s/件</span>
      </div>
      <div class="in">
        最小节拍：
        <span>{{data.clockMin}}s/件</span>
      </div>
      <div class="in">
        标准节拍：
        <span>{{data.clock0}}s/件</span>
      </div>
      <div class="in">
        平均节拍：
        <span>{{data.clockAvg}}s/件</span>
      </div>
    </div> -->
    <beatanalysis ref="beatanalysis"
                  :beatshow="beatshow"
                  :datac="data"/>
  </div>
</template>

<script lang="ts">
import {ref, onMounted, watch, computed} from "@vue/composition-api";
import {useLoading} from "web-toolkit/src/service/use-decrator";
import beatanalysis from '../beat/beatanalysis.vue'
import {ClockPE} from '@/dao/ae';
export default {
  components: {
    beatanalysis,
  },
  setup() {
    const loading = ref(false);
    const chart = ref<any>();
    const beatshow = ref<boolean>(false);
    const data = ref<any>([])

    async function query(time11: any, time22: any, class11: any, class22: any) {
      data.value=await ClockPE({
          start: time11.getTime(),
          end: time22.getTime(),
         });
      // data.value = [];
      // let data1 = await ClockPE({
      //   start: time11.getTime(),
      //   end: time22.getTime(),
      // });
      // for (let i = 0; i < data1.clocks[0].list.length; i++) {
      //   data.value.push([new Date(data1.clocks[0].list[i].time), data1.clocks[0].list[i].duration])
      // }
      console.log(data.value)
    }

    onMounted(
      useLoading(loading, async function () {
      })
    );
    return {
      chart,
      loading,
      open,
      data,
      beatshow,
      query: useLoading(loading, query),
    };
  }
};
</script>

<style lang="scss" scoped>
.beat {
  margin-top: 20px;
  margin-bottom: 10px;
  .one {
    width: 233px;
    height: 102px;
    border: solid 1px #eeeeee;
    background-color: #e3effe;

    .box {
      text-align: center;
      p {
        font-size: 14px;
        color: rgba(0, 0, 0, 0.4);
      }

      .num {
        font-size: 30px;
        color: rgba(0, 0, 0, 0.8);
        margin-top: 5px;
      }
    }
  }
  .two {
    width: 233px;
    height: 102px;
    border: solid 1px #eeeeee;
    background-color: #fff;

    .box {
      text-align: center;
      p {
        font-size: 14px;
        color: rgba(0, 0, 0, 0.4);
      }

      .num {
        font-size: 30px;
        color: rgba(0, 0, 0, 0.8);
        margin-top: 5px;
      }
    }
  }
}

.content {
  height: 50px;
  background-color: #ffffff;
  color: #333333;
  margin-bottom: 10px;
  .in {
    font-size: 14px;
    span {
      font-size: 16px;
      color: red;
      font-weight: bold;
    }
  }
}

</style>
