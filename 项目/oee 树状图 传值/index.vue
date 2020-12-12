
<template>
  <div v-loading="loading" style="padding: 10px 0 0 20px">
    <product-type-select style="margin-bottom:10px" :productSelcet="product" ref="typeSelect"></product-type-select>
    <div class="select flex">
      <div class="flex"  v-if="flag===0">
        <div class="flex align-center">
          <div >日期范围：</div>
          <el-date-picker
            type="daterange"  
            v-model="datetimeRange"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            style="margin-left: 10px;margin-right:10px"
          ></el-date-picker>
          </div>
      <div>
        <el-radio-group v-model="select">
          <el-radio-button label="日"></el-radio-button>
          <el-radio-button label="班次"></el-radio-button>
          <el-radio-button label="周"></el-radio-button>
        </el-radio-group>
      </div>
      </div>
      <div class="flex">
        <div v-if="(flag===1 || flag===2 || flag===3 )&& (select1==='日') " class="flex" style="margin:0px 10px 0 0;">
          <div class="flex align-center"> 
            <div >日期范围：</div>
            <el-date-picker
              type="daterange"
              v-model="datetimeRange1"
              start-placeholder="开始日期"
              end-placeholder="结束日期"
              style="margin-left: 10px;margin-right:10px"
            ></el-date-picker>
          </div>
        </div>
        <div v-if="(flag===1 || flag===2 || flag===3) && (select1==='班次' )" class="flex" style="margin:0px 10px 0 10px;">
        <div>
          <el-date-picker v-model="timeValue1" type="date" placeholder="选择开始日期">
          </el-date-picker>
          <el-select v-model="classValue1" placeholder="请选择班次" style="width:120px">
              <el-option
                v-for="item in classOption"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
            </el-select>   -
          <el-date-picker v-model="timeValue2" type="date" placeholder="选择结束日期" >
          </el-date-picker>
          <el-select v-model="classValue2" placeholder="请选择班次"  style="margin-right:20px;width:120px">
              <el-option
                v-for="item in classOption"
                :key="item.value"
                :label="item.label"
                :value="item.value">
              </el-option>
          </el-select>
        </div>
      </div>
      <div v-if="flag===1 || flag===2 || flag===3">
          <el-radio-group v-model="select1">
          <el-radio-button label="日"></el-radio-button>
          <el-radio-button label="班次"></el-radio-button>
        </el-radio-group>
      </div>
    </div>
      <el-button type="primary" style="margin-left: 10px"  size="small" @click="query()">查询</el-button>
    </div>
    <div style="margin-top:20px">
      <el-button @click="()=>{flag=0;query()}" style="width:100px" :type="flag===0?'primary':''">OEE统计</el-button>
      <el-button @click="()=>{flag=1;query()}" style="width:100px" :type="flag===1?'primary':''">AE统计</el-button>
      <el-button @click="()=>{flag=2;query()}" style="width:100px" :type="flag===2?'primary':''">PE统计</el-button>
      <el-button @click="()=>{flag=3;query()}" style="width:100px" :type="flag===3?'primary':''">QE统计</el-button>
    </div>

    <oee ref="oee" v-show="flag===0"  @jump="jump" :workshop="workshop" style="padding-right: 20px"/>
    <ae  ref="ae" v-show="flag===1" style="padding-right: 20px"/>
    <pe  ref="pe" v-show="flag===2" style="padding-right: 20px"/>
    <qe  ref="qe" v-show="flag===3" style="padding-right: 20px"/>
  </div>
</template>
<script lang="ts">
import { ref, onMounted, Ref, watch } from "@vue/composition-api";
import { useLoading } from "web-toolkit/src/service/use-decrator";

import oee from './oee.vue'
import ae from './ae.vue'
import pe from './pe.vue'
import qe from './qe.vue'

export default {
  components:{
    oee,
    ae,
    pe,
    qe
  },
  setup() {
    const loading = ref(false);
    const flag = ref<any>(0);
    const radio = ref<any>('1');
    const timeValue1 = ref<any>(); //ae pe qe界面的日期值 select=1 选择的是日期
    const timeValue2 = ref<any>();
    const typeSelect = ref<any>();
    const timeValue3 = ref<any>();
    const timeValue4 = ref<any>();
    const select = ref<any>('日');
    const select1 = ref<any>('日');
    const datetimeRange: Ref<any[]> = ref([]); //oee界面的选择框
    const datetimeRange1: Ref<any[]> = ref([]); //oee界面的选择框
    const chart = ref<any>();
    const product = ref<any>({product:null, plan:null,programme:null});
    const oee = ref<any>();
    const ae = ref<any>();
    const pe = ref<any>();
    const qe = ref<any>();
    const classValue1 = ref<any>();
    const classValue2 = ref<any>();
    const classOption = ref<any>([
      {
        value:'1',
        label:'白班'
      },
      {
        value:'2',
        label:'晚班'
      },
    ]);
    function jump(flag1:any,time:any,select11:any){
      flag.value= flag1;
      datetimeRange1.value[0]=time
      datetimeRange1.value[1]=time
      timeValue1.value=time
      timeValue2.value=time
      select1.value=select11 
      query()
    }
    async function query(){
      console.log(product.value)
      if(flag.value===0){   //oee
        if(select.value==='日'){
          if(datetimeRange.value.length===0){
            datetimeRange.value=[new Date(2020,9,20),new Date(2020,9,22)]
          }
          oee.value.query(datetimeRange.value[0], datetimeRange.value[1]);
        }else{
          oee.value.query(datetimeRange.value[0], datetimeRange.value[1],1,1);
        }
      }else if(flag.value===1){ //ae
        if(select1.value==='日'){
          if(datetimeRange1.value.length===0){
            datetimeRange1.value=[new Date(2020,9,20),new Date(2020,9,22)]
          }
          ae.value.query( datetimeRange1.value[0], datetimeRange1.value[1], classValue1.value,classValue2.value );
        }
        else {
          ae.value.query( timeValue1.value? timeValue1.value: new Date(2020,9,20),timeValue2.value? timeValue2.value: new Date(2020,9,22),classValue1.value,classValue2.value );
        }
      }else if(flag.value===2){ //pe
        if(select1.value==='日'){
          if(datetimeRange1.value.length===0){
            datetimeRange1.value=[new Date(2020,9,20),new Date(2020,9,22)]
          }
          pe.value.query( datetimeRange1.value[0], datetimeRange1.value[1], classValue1.value,classValue2.value );
        }
        else {
          pe.value.query( timeValue1.value? timeValue1.value: new Date(2020,9,20),timeValue2.value? timeValue2.value: new Date(2020,9,22),classValue1.value,classValue2.value );
        }      }
        else{ //qe
          if(select1.value==='日'){
            if(datetimeRange1.value.length===0){
              datetimeRange1.value=[new Date(2020,9,20),new Date(2020,9,22)]
          }
            qe.value.query( datetimeRange1.value.length>0?datetimeRange1.value[0] : new Date(2020,9,20), datetimeRange1.value.length>0?datetimeRange1.value[1]:new Date(2020,9,22), classValue1.value,classValue2.value );
          }
          else {
            qe.value.query( timeValue1.value? timeValue1.value: new Date(2020,9,20),timeValue2.value? timeValue2.value: new Date(2020,9,22),classValue1.value,classValue2.value );
          }
      }
    }
    onMounted(
      useLoading(loading, async function () {
        query();
        await typeSelect.value.query()
      })
    );
    return {
      loading,flag,datetimeRange,chart,product,radio,
      timeValue1,timeValue2,timeValue3,timeValue4,select,select1,classValue1,classValue2,classOption,
      jump,query,oee,ae,pe,qe,datetimeRange1,typeSelect
    };
  },
};
</script>
<style lang="scss" scoped>

</style>
