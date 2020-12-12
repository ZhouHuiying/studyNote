<template>
  <div v-loading="loading" class="out flex">
    <div class="left">
      <div class="card" style="width:100%;">
        <div class="card_title" >
          <span>异常排名TOP5</span>
        </div>
        <div class="one">
        <div class="reason flex" v-for="item of influence" :key="item.id">
          <div class="number">{{item.id}}</div>
          <div class="item">{{ item.value }}</div>
        </div>
      </div>
    </div> 
      
      <div class="card" style="width:100%;margin-top:15px;">
        <div class="card_title" >
          <span>产线开关机异常时刻表</span>
        </div>
        <div class="two">
          <div class="table">
            <el-table :data="tableData" style="width: 100%">
              <el-table-column prop="time" label="时间" align="center">
              </el-table-column>
              <el-table-column prop="startTime" label="开机时间" align="center">
              </el-table-column>
              <el-table-column prop="closeTime" label="关机时间" align="center">
              </el-table-column>
              <el-table-column prop="info" label="异常描述" align="center">
                <div slot-scope="{ row }" style="color: red; font-weight: 700">
                  {{ row.info }}
                </div>
              </el-table-column>
            </el-table>
          </div>
      </div>
    </div>
    </div>
    <div class="right card">
      <div class="lineChart">
        <v-chart
          ref="chartTop"
          class="chart"
          :autoresize="true"
          :options="optionFirst"
        ></v-chart>
      </div>
      <div class="lineChart">
        <v-chart
          ref="chart2"
          class="chart"
          :autoresize="true"
          :options="optionTwo"
        ></v-chart>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { ref, onMounted, Ref, watch } from "@vue/composition-api";
import { statusMap } from "@/utils/device-utils";
import { useLoading } from "web-toolkit/src/service/use-decrator";
import { OeeList } from "@/dao/oee";
import { formatDateTime } from "web-toolkit/src/utils";
import echarts from 'echarts'
export default {
  props: {
    workshop: {
      type: String,
      default: "",
    },
  },
  setup(props: any, ctx: any) {
    const loading = ref(false);
    const chartTop = ref<any>();
    const chart2 = ref<any>();
    const optionFirst = ref<any>({});
    const optionTwo = ref<any>({});
    const show = ref<any>(false);
    const time1 = ref<any>("");
    const time2 = ref<any>("");
    const timeStartShift = ref<any>("");
    const timeEndShift = ref<any>("");
    const oeeData = ref<any>([]);
    const aeData = ref<any>([]);
    const peData = ref<any>([]);
    const qeData = ref<any>([]);
    const testData1 = ref<any>([
			{
				value:45,
        name:"原因1：无心磨1故障",
        children:[
					{
						value:7,
            name:"原因1-1：无心磨1故障",
          },
          {
						value:8,
            name:"原因1-2：无心磨1故障",
          },
          {
						value:15,
            name:"原因1-3：无心磨1故障",
					},
					{
						value:25,
            name:"原因1-4：上料处待料",
          },
				]
			},
			{
				value:30,
        name:"原因2",
				children:[
					{
						value:10,
            name:"原因2-1：端面磨故障",
					},
					{
						value:5,
						name:"原因2-2：无心磨2设备调整"
          },
          {
						value:7,
            name:"原因2-3：端面磨故障",
					},
					{
						value:8,
						name:"原因2-4：无心磨2设备调整"
          },
				]
			},
			{
				value:25,
        name:"原因3",
        children:[
					{
						value:10,
            name:"原因3-1：无心磨1设备调整",
					},
					{
						value:6,
            name:"原因3-2：自动检测机设备调整",
          },
          {
						value:9,
            name:"原因3-3：自动检测机设备调整",
          },
				]
			}
		])
		const influence = ref<any>([
      {
        id:1,
        value:"无心磨1故障，时长：3h，占OEE损失百分比为 10%",
      },
      {
        id:2,
        value: "上料处待料，时长：2h，占OEE损失百分比为 9.1%",
      },
      {
        id:3,
        value:"端面磨故障，时长：1h40min，占OEE损失百分比为 8.9%",
      },
      {
        id:4,
        value:"无心磨2设备调整，时长：1h10min，占OEE损失百分比为 7.2%",
      },
      {
        id:5,
        value:"自动检测机设备调整，时长：50min，占OEE损失百分比为 5.2%",
      },
    ]);
    const reasons = ref<any>([
      {
        status: "正常",
        time1: "2020:09:23",
        time2: "2020:09:40",
        duration: "11",
      },
      {
        status: "异常",
        time1: "2020:10:23",
        time2: "2020:10:40",
        duration: "22",
      },
    ]);
    const tableData = ref<any>([
      {
        time: "2020-10-23",
        startTime: "7:50:00",
        closeTime: "10:40:00",
        info: "延迟开机",
      },
      {
        time: "2020-10-24",
        startTime: "8:00:00",
        closeTime: "11:00:00",
        info: "延迟开机",
      },
      {
        time: "2020-10-25",
        startTime: "14:10:00",
        closeTime: "16:50:00",
        info: "延迟开机",
      },
    ]);
    async function query(time11: any, time22: any, class11: any, class22: any) {
      time1.value = formatDateTime(time11);
      time2.value = formatDateTime(time22);
      timeStartShift.value = class11;
      timeEndShift.value = class22;
      await query1();
    }
    async function query1() {
      let list = await OeeList({
        timeStart: time1.value,
        timeEnd: time2.value,
        timeStartShift: timeStartShift.value,
        timeEndShift: timeEndShift.value,
      });
      oeeData.value = [];
      aeData.value = [];
      peData.value = [];
      qeData.value = [];
      for (var i = 0; i < list.length; i++) {
        oeeData.value.push([list[i].day, list[i].oee]);
        aeData.value.push([list[i].day, list[i].ae]);
        peData.value.push([list[i].day, list[i].pe]);
        qeData.value.push([list[i].day, list[i].qe]);
      }
      console.log(oeeData.value)
      optionFirst.value = {
        title: {
          // text: ''
        },
        tooltip: {
          trigger: "axis",
        },
        color: ["#4472C5", "#54ACF9", "#9278E6", "#4CD7AC"],
        legend: {
          textStyle: {
            fontSize: 16,
          },
          data: ["OEE", "AE", "PE", "QE"],
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          containLabel: true,
        },
        toolbox: {
          feature: {
            // saveAsImage: {}
          },
        },
        xAxis: {
          type: "category",
          name: "日期",
          nameTextStyle: {
            fontWeight: "bold",
            fontSize: 16,
          },
        },
        yAxis: {
          type: "value",
        },
        visualMap: {
          top: 20,
          right: 10,
          show: false,
          seriesIndex: 0,
          pieces: [
            {
              gt: 0,
              lte: 43,
              color: "rgb(255, 0, 0)",
            },
            {
              gt: 43,
              color: "rgb(97, 165, 232)",
            },
          ],
          outOfRange: {
            color: "rgb(97, 165, 232)",
          },
        },
        series: [
          {
            name: "OEE",
            type: "line",
            data: oeeData.value,
            markLine: {
              label: {
                formatter: "计划值",
              },
              data: [
                {
                  yAxis: 43,
                },
              ],
            },
          },
          {
            name: "AE",
            type: "bar",
            data: aeData.value,
          },
          {
            name: "PE",
            type: "bar",
            data: peData.value,
          },
          {
            name: "QE",
            type: "bar",
            data: qeData.value,
          },
        ],
      };
      optionTwo.value = {
        title: {
          text: "异常详情",
          left: "center",
        },
        tooltip: {
          formatter: function (info: any) {
            return info.name + "：" + info.value
          },
        },
        series: [
          {
            name: "异常详情",
            type: "treemap",
            visibleMin: 300,
            label: {
              show: true,
              formatter: "{b}",
            },
            itemStyle: {
              borderColor: "#fff",
            },
            levels: getLevelOption(),
            data: testData1.value,
            nodeClick : 'false',
            roam:false
          },
        ],
      };
    }
    function getLevelOption() {
      return [
        {
          itemStyle: {
            borderWidth: 0,
            gapWidth: 5,
          },
          color:['#54ACF9','#9278E6','#4CD7AC'],
          colorAlpha:[0.3,0.5],
          // colorMappingBy: 'value',

        },
        {
          itemStyle: {
            gapWidth: 1,
          },
          colorMappingBy: 'value',
        },
        {
          colorSaturation: [0.35, 0.5],
          itemStyle: { 
            gapWidth: 1,
						borderColorSaturation: 0.6,
          },
          colorMappingBy: 'value',
        },
      ];
    }
    onMounted(
      useLoading(loading, async function () {
        chartTop.value.chart.on("click", function (param: any) {
          if (param.componentIndex === 1) {
            ctx.emit("jump", 1, new Date("2020" + "/" + param.value[0]), "日");
          } else if (param.componentIndex === 2) {
            ctx.emit("jump", 2, new Date("2020" + "/" + param.value[0]), "日");
          } else {
            ctx.emit("jump", 3, new Date("2020" + "/" + param.value[0]), "日");
          }
        });
        // query1();
      })
    );
    return {
      loading,
      time1,
      time2,
      timeStartShift,
      timeEndShift,
      getLevelOption,
      // formatUtil,
      query: useLoading(loading, query),
      query1,
      show,
      oeeData,
      aeData,
      peData,
      qeData,
      reasons,
      chartTop,
      optionFirst,
      tableData,
      influence,
      chart2,
      optionTwo,
			testData1
    };
  },
};
</script>
<style lang="scss" scoped>
.out {
  margin-top: 20px;
  .left {
    width: 23%;
    min-width: 250px;
    .one {
      background-color: #fff;
      .reason {
        width: 90%;
        margin: 0 auto;
        padding: 8px;
        .number{
          background-color: #f05454;
          color:#fff;
          width:20px;
          min-width:20px;
          height:20px;
          min-height:20px;
          text-align: center;
          line-height: 20px;
          border-radius: 50%;
        }
        .item {
          height: 50px;
          color: gray;
          margin-left: 8px;
        }
      }
    }
    .two {
      background-color: #fff;
      min-width:200px;
      .top {
        height: 40px;
        line-height: 40px;
        position: relative;
        .title {
          font-size: 16px;
          font-weight: 600;
          margin-left: 8px;
        }
        .detail {
          position: absolute;
          right: 15px;
          color: #66b1ff;
        }
      }
      .table {
        margin-top: 10px;
      }
    }
  }
  .right {
    width: 70%;
    background-color: #fff;
    margin-left: 20px;
    height: 78vh;
    .lineChart {
      margin-top: 20px;
      background-color: #fff;
      .chart {
        width: 100%;
        height: 350px;
      }
    }
  }
}
</style>
