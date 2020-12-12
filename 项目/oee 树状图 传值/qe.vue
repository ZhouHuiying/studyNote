<template>
  <div v-loading="loading" class="out">
    <div class="beat flex between">
      <div class="one flex center">
        <div class="box">
          <p>QE</p>
          <div class="num">{{qe.qe}}</div>
        </div>
      </div>
      <div class="two flex center">
        <div class="box">
          <p>产量</p>
          <div class="num">{{qe.output}}</div>
        </div>
      </div>
      <div class="two flex center">
        <div class="box">
          <p>废品数</p>
          <div class="num">{{qe.rejects}}</div>
        </div>
      </div>
    </div>
    <div class="flex between" >
      <!-- 不良品类型统计 -->
      <div style="margin-top:10px;width:39%;height:100%" class="card">
        <div class="card_title " style="width:100%">
        <div style="width:50%" >不良品类型统计</div>
      </div>
      <div class="wastrel" style="width:100%;" >
        <v-chart
        style="height:200px;width:100%;padding-top:-30px"
          ref="chart1"
          class="chart1"
          :autoresize="true"
          :options="option1"
        >
        </v-chart>
      </div>
      </div>
      <!-- 不良品原因分析 -->
      <div style="margin-top:10px;width:59%;height:100%" class="card">
        <div class="card_title flex" style="width:100%">
        <div >不良品原因分析</div>
      </div>
      <div class="reason" style="width:100%;">
      <v-chart
          style="height:200px;width:100%"
          ref="chart2"
          class="chart2"
          :autoresize="true"
          :options="option2"
        >
        </v-chart>
      </div>
      </div>
    </div>
    <!-- 质量SPC检测 -->
      <div style="margin-top:10px;width:100%" class="card">
        <div class="card_title flex" style="width:100%">
        <div >质量SPC检测</div>
      </div>
      <div class="quality " style="width:100%" >
        <!-- <el-radio-group v-model="limit.type">
      <el-radio label="XRChart">均值 - 极差图</el-radio>
      <el-radio label="XSChart">均值 - 标准差图</el-radio>
      <el-radio label="MRChart">中位数 - 极差图</el-radio>
      <el-radio label="XMRChart">单值 - 移动极差图</el-radio>
    </el-radio-group> -->
    <div class="flex" style="margin-bottom:10px">
    <div >
    <el-radio-group v-model="radio1">
      <el-radio-button label="XRChart">均值 - 极差图</el-radio-button>
      <el-radio-button label="XSChart">均值 - 标准差图</el-radio-button>
      <el-radio-button label="MRChart">中位数 - 极差图</el-radio-button>
      <el-radio-button label="XMRChart">单值 - 移动极差图</el-radio-button>
    </el-radio-group>
    </div>
    <div style="margin:0 20px">
      <span v-show="limit.type !== 'XMRChart'">子组容量：</span>
      <el-input-number v-show="limit.type !== 'XMRChart'" v-model="limit.subgroups" :min="0"/>
    </div>
    <div><el-button type="primary" @click="query2">查询</el-button></div>
    </div>
    <!-- <div class="flex little-space align-center wrap">
      <span v-show="limit.type !== 'XMRChart'">子组容量：</span>
      <el-input-number v-show="limit.type !== 'XMRChart'" v-model="limit.subgroups" :min="0"/>
      <span v-show="limit.type === 'XMRChart'">单值采样频率：</span>
      <el-input-number v-show="limit.type === 'XMRChart'" v-model="limit.samples" :min="10"/>
      <el-button type="primary" @click="query">查询</el-button>
    </div> -->
    <div class="flex between" style="width:100%">
      <div class="flex column between" style="height:350px;width:14%;padding:2%;border:1px solid #DFDFDF;border-radius: 2px;box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04)">
        <el-checkbox-group v-model="checkboxGroup"  v-for="(item,i) in checkboxGroup1" :key="i" >
          <div class="check"><el-checkbox :label="item" border></el-checkbox></div>
        </el-checkbox-group>
      </div>
      <div class="nomsg" v-if="charts.length===0" style="width:84%">
        暂无数据
      </div>
  <div  v-else class="flex column" style="width:84%">
    <div v-for="chart of charts" :key="chart.name" v-if="checkboxGroup.indexOf(chart.name)>=0" >
      <h2>{{ chart.name }}</h2>
        <div class="flex column" >
          <div><v-chart :autoresize="true" style=" height: 300px; width:100%" :options="chart.subchart1"/></div>
          <div><v-chart :autoresize="true" style=" height: 300px;width:100%" :options="chart.subchart2"/></div>
      </div>
    </div>
    </div>
  </div>
  </div>
</div>
</div>
</template>
<script lang="ts">
import { ref, reactive, Ref, onMounted, watch,computed } from '@vue/composition-api';
import { EChartOption } from 'echarts';
import { isArray, formatDateTime } from 'web-toolkit/src/utils';
import { validateDateRange } from 'web-toolkit/src/utils/validator';
import {postService} from 'web-toolkit/src/case-main';
import { useLoading} from 'web-toolkit/src/service';
import {QeList} from '@/dao/qe';
interface ILimit {
  type: 'XRChart' | 'XSChart' | 'MRChart' | 'XMRChart';
  datetimeRange: Date[];
  subgroups: number;
  samples: number;
}
export default {
  setup(props:any,ab:any) {
    const loading = ref(false);
    const radio1=ref<any>('XRChart')
    const option1=ref<any>()
    const option2=ref<any>()
    const chart1 = ref<any>();
    const chart2 = ref<any>();
    const qe = ref<any>();
    const reasonAnalysis = ref<any>();
    const time1=ref<any>()
    const time2=ref<any>()
    const table1 = ref<any>()
    const class1=ref<any>()
    const class2=ref<any>()
    const checkboxGroup1 = ref<any>([]);
    const checkboxGroup = ref<any>([]);
    const limit = reactive({
      type: 'XRChart',
      datetimeRange: [new Date(Date.now() - 4 * 24 * 3600000), new Date()],
      subgroups: 10,
      samples: 100,
    }) as ILimit;
    const charts: Ref<any> = ref([]);
    function splitChart(options: EChartOption, { max, min, mid, minColor, midColor, maxColor }: Partial<ISplitChartOptions>) {
      let { xAxis } = options;
      const { series } = options;
      minColor = minColor || '#d82626';
      midColor = midColor || '#1890ff';
      maxColor = maxColor || '#d82626';
      if (!series) {
        return options;
      }
      options.xAxis = isArray(xAxis) ? xAxis : (xAxis ? [xAxis] : []);
      xAxis = options.xAxis;
      const splitxAxis = { type: 'value', position: 'bottom', axisLabel: { show: false }, splitLine: { show: false }, axisPointer: { show: false } };
      options.visualMap = [{
        type: 'piecewise',
        pieces: [
          { max: min, color: minColor },
          { min: max, color: maxColor },
          { min, max, color: midColor },
        ],
        show: false,
      }];
      return options;
    }
    async function query1(){
      let data=await QeList({timeStart:time1.value,timeEnd:time2.value,timeStartShift:class1.value,timeEndShift:class2.value,type:radio1.value})
        console.log(data)
        // 标题
        qe.value=data.qe
        // 不良品原因分析
        reasonAnalysis.value=data.statistics
        const data1=[]
        for(let i=0;i<reasonAnalysis.value.length;i++){
          data1.push([reasonAnalysis.value[i].x,reasonAnalysis.value[i].y])
        }
        console.log(radio1.value)
        option1.value = {
          tooltip: {
              trigger: 'item',
              formatter: '{a} <br/>{b} : {c} ({d}%)'
          },
          grid: {
              left: 0,
              right: '5%',
              bottom: '4%',
              top:0,
          },
          legend: {
                orient: 'vertical',
                left: 'right',
                label: {
                    formatter: '{b}: {@2012} ({d}%)'
                },
                data: ['影像检测不合格', '扭力不合格', '长度尺寸不合格', '轴径尺寸不合格']
            },
          series: [
              {
                  name: '不良品类型占比',
                  type: 'pie',
                  radius: '55%',
                  center: ['50%', '60%'],
                  label: {
                    formatter: '{b}: {@2012} ({d}%)'
                },
                  data: [
                      {value: qe.value.imageUnqualified, name: '影像检测不合格'},
                      {value: qe.value.torsionUnqualified, name: '扭力不合格'},
                      {value: qe.value.lengthUnqualified, name: '长度尺寸不合格'},
                      {value: qe.value.shaftDiaUnqualified, name: '轴径尺寸不合格'},
                  ],
                  emphasis: {
                      itemStyle: {
                          shadowBlur: 10,
                          shadowOffsetX: 0,
                          shadowColor: 'rgba(0, 0, 0, 0.5)'
                      }
                  }
              }
          ]
        };
        option2.value = {
            color: ['#3398DB'],
            tooltip: {
                trigger: 'axis',
                axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                    type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                }
            },
            grid: {
                left: '3%',
                right: '4%',
                bottom: '3%',
                containLabel: true
            },
            xAxis: [
                {
                    type: 'category',
                    axisTick: {
                        alignWithLabel: true
                    }
                }
            ],
            yAxis: [
                { splitLine:{ show:false },
                    type: 'value'
                }
            ],
            series: [
                {
                    name: '不良品数量',
                    type: 'bar',
                    barWidth: '60%',
                    data:data1
                }
            ]
        };
    }
    async function query2(){
    let data=await QeList({timeStart:time1.value,timeEnd:time2.value,timeStartShift:class1.value,timeEndShift:class2.value,type:radio1.value})
    checkboxGroup1.value=[]
      for(let i=0;i<data.spc.length;i++){
        checkboxGroup1.value.push(data.spc[i].key)
      }
      checkboxGroup.value= checkboxGroup1.value
      for (const e of data.spc) {
        if (!e.xSInfos) {
          charts.value = [];
          return ;
        }
      }
      charts.value = data.spc.map((chart: any) => {
        return {
          name: chart.key,
          subchart1: splitChart({
            tooltip: {
              trigger: 'axis',
              formatter(params: any) {
                params = params[0];
                const dt = formatDateTime(new Date(params.data.dt));
                const val = params.value;
                return `
                  <div>上限：${chart.uclx}</div>
                  <div>均值：${chart.averX}</div>
                  <div>下限：${chart.lclx}</div>
                  <div>日期：${dt}</div>
                  <div>${params.marker}当前值：${val}</div>
                `;
              },
            },
            grid: {
              left: "3%",
              right: "4%",
              bottom: "10%",
              top:'3%',
              containLabel: true,
            },
            xAxis: {
              data: chart.xSInfos.map((item: any, i: number) => i + 1),
            },
            // @ts-ignore
            yAxis: {
              // @ts-ignore
              min: (value) => (Math.min(value.min, chart.lclx) - 0.01).toFixed(2),
              // @ts-ignore
              max: (value) => (Math.max(value.max, chart.uclx) + 0.01).toFixed(2),
            },
            // @ts-ignore
            series: [{
              type: 'line',
              markPoint: {
                symbolSize: 40,
                data: [
                  {type: 'max', name: '最大值'},
                  {type: 'min', name: '最小值'},
                ],
              },
              markLine: {
                // silent: true,
                precision: 4,
                data: [
                  { yAxis: chart.lclx },
                  { yAxis: chart.uclx },
                  {
                    yAxis: chart.averX,
                    lineStyle: {
                      color: '#1890ff',
                    },
                  },
                ],
              },
              data: chart.xSInfos.map((item: any) => ({ value: parseFloat(item.val.toFixed(4)), dt: item.dt })),
            }],
          }, { min: chart.lclx, mid: chart.averX, max: chart.uclx }),
          subchart2: splitChart({
            tooltip: {
              trigger: 'axis',
              formatter(params: any) {
                params = params[0];
                const dt = formatDateTime(new Date(params.data.dt));
                const val = params.value;
                return `
                  <div>上限：${chart.uclr}</div>
                  <div>均值：${chart.averR}</div>
                  <div>下限：${chart.lclr}</div>
                  <div>日期：${dt}</div>
                  <div>${params.marker}当前值：${val}</div>
                `;
              },
            },
            grid: {
              left: "4%",
              right: "4%",
              bottom: "10%",
              top:'3%',
              containLabel: true,
            },
            xAxis: {
              data: chart.rMRInfos.map((item: any, i: number) => i + 1),
            },
            // @ts-ignore
            yAxis: {
              // @ts-ignore
              min: (value) => (Math.min(value.min, chart.lclr) - 0.01).toFixed(2),
              // @ts-ignore
              max: (value) => (Math.max(value.max, chart.uclr) + 0.01).toFixed(2),
            },
            // @ts-ignore
            series: [{
              type: 'line',
              markPoint: {
                symbolSize: 40,
                data: [
                  {type: 'max', name: '最大值'},
                  {type: 'min', name: '最小值'},
                ],
              },
              markLine: {
                // silent: true,
                precision: 4,
                data: [
                  { yAxis: chart.lclr },
                  { yAxis: chart.uclr },
                  {
                    yAxis: chart.averR,
                    lineStyle: {
                      color: '#1890ff',
                    },
                  },
                ],
              },
              data: chart.rMRInfos.map((item: any) => ({ value: item.val, dt: item.dt })),
            }],
          }, { min: chart.lclr, mid: chart.averR, max: chart.uclr }),
        };
      });
    }
    async function query(time11:any,time22:any,class11:any,class22:any) {
      //index页面的query函数之后执行对应页面的query函数，aepeqe页面的query函数接受index传过来
      //的参数，比如时间timerange或者班次
      time1.value=formatDateTime(time11)
      time2.value=formatDateTime(time22)
      class1.value=class11
      class2.value=class22
      console.log(time1.value)
      console.log(time2.value)
      await query1()
    }
    // watch(() => limit.type, useLoading(loading,query1), { lazy: true });
    onMounted(
      useLoading(loading, async function () {
      query1() ,query2()
      })
    );
    return {
      loading,option1,option2,chart1,chart2,query1,limit,
      query: useLoading(loading, query),time1,time2,class1,class2,
      charts,splitChart,radio1,checkboxGroup,checkboxGroup1,reasonAnalysis,qe,query2,
    };
  },
};
interface ISplitChartOptions {
  min: number;
  minColor: string;
  mid: number;
  midColor: string;
  max: number;
  maxColor: string;
}
</script>
<style lang="scss" scoped>
.out{
  min-width: 800px;
  .beat {
    margin-top: 10px;
  .one {
    width: 25%;
    height: 102px;
    border: solid 1px #eeeeee;
    background-color: #D6E3F5;
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
     width: 25%;
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
  .nomsg{
    text-align: center;
    color: grey;
    font-size: 2rem;
    margin-top: 1rem;
  }
}

}

</style>
