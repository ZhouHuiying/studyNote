<template>
  <div v-loading="loading">
    <div class="all">
      <!-- 运行条 -->
      <div class="card">
        <div class="flex title ">
          <div class="pe flex column center">
            <div class="title1">AE</div>
            <div class="number">{{ number.ae }}</div>
          </div>
          <div class="time">
            <svg width="100%" height="100%" version="1.1" xmlns="http://www.w3.org/2000/svg">
              <!-- 左竖线 -->
              <line :x1="occupy.plan+'%'" y1="0" :x2="occupy.plan+'%'" y2="22"
                    style="stroke: rgb(99, 99, 99); stroke-width: 2"/>
              <!-- //中间文字 -->
              <text :x="occupy.plan+(occupy.work+occupy.notPlan)/2-6+'%'" y="14" fill="black"
                    v-if="parseInt(occupy.workTime)>1440">
                负荷时间：{{ (occupy.workTime / 60 / 24).toFixed(0) + '天' + (occupy.workTime / 60 % 24).toFixed(0) + 'h' + occupy.workTime % 60 + 'min' }}
              </text>
              <text :x="occupy.plan+(occupy.work+occupy.notPlan)/2+'%'" y="14" fill="black"
                    v-if="occupy.workTime<1440&&occupy.workTime>60">
                负荷时间：{{ (occupy.workTime / 60).toFixed(0) + 'h' + occupy.workTime % 60 + 'min' }}
              </text>
              <text :x="occupy.plan+(occupy.work+occupy.notPlan)/2+'%'" y="14" fill="black"
                    v-if="occupy.workTime<60&&occupy.workTime>0">
                负荷时间：{{ occupy.workTime + 'min' }}
              </text>
              <!-- 第一个矩形 -->
              <rect :width="occupy.plan + '%'" :x="0" y="22" height="37"
                    style="fill: rgb(121, 121, 121);stroke-width: 1;stroke: rgb(121, 121, 121);"/>
              <line :x1="occupy.plan / 2 + '%'" y1="59" :x2="occupy.plan / 2 + '%'" y2="75"
                    style="stroke: rgb(99, 99, 99); stroke-width: 2"/>
              <text :x="occupy.plan / 2 -2+ '%'" y="90" fill="black" v-if="occupy.planStop>0">计划停机时间</text>
              <text :x="occupy.plan / 2 -2+ '%'" y="114" fill="black" v-if="parseInt(occupy.planStop)>1440">
                {{ (occupy.planStop / 60 / 24).toFixed(0) + '天' + (occupy.planStop / 60 % 24).toFixed(0) + 'h' + occupy.planStop % 60 + 'min' }}
              </text>
              <text :x="occupy.plan / 2 -2+ '%'" y="114" fill="black" v-if="occupy.planStop<1440&&occupy.planStop>60">
                {{ (occupy.planStop / 60).toFixed(0) + 'h' + occupy.planStop % 60 + 'min' }}
              </text>
              <text :x="occupy.plan / 2 -2+ '%'" y="114" fill="black" v-if="occupy.planStop<60&&occupy.planStop>0">
                {{ occupy.planStop + 'min' }}
              </text>
              <!-- 第二个矩形 -->
              <rect :width="occupy.notPlan + '%'" :x="occupy.plan + '%'" y="22" height="37"
                    style="fill: rgb(255, 204, 0);stroke-width: 1;stroke: rgb(121, 121, 121);"/>
              <line :x1="occupy.notPlan / 2 + occupy.plan + '%'" y1="59" :x2="occupy.notPlan / 2 + occupy.plan + '%'"
                    y2="75" style="stroke: rgb(99, 99, 99); stroke-width: 2"/>
              <text :x="occupy.plan + occupy.notPlan / 2-5+'%'" y="90" fill="black" v-if="occupy.accidentStop>0">
                非计划停机时间
              </text>
              <text :x="occupy.plan +occupy.notPlan / 2-2+ '%'" y="114" fill="black"
                    v-if="parseInt(occupy.accidentStop)>1440">
                {{ (occupy.accidentStop / 60 / 24).toFixed(0) + '天' + (occupy.accidentStop / 60 % 24).toFixed(0) + 'h' + occupy.accidentStop % 60 + 'min' }}
              </text>
              <text :x="occupy.plan +occupy.notPlan / 2-2+ '%'" y="114" fill="black"
                    v-if="occupy.accidentStop<1440&&occupy.accidentStop>60">
                {{ (occupy.accidentStop / 60).toFixed(0) + 'h' + occupy.accidentStop % 60 + 'min' }}
              </text>
              <text :x="occupy.plan +occupy.notPlan / 2-2+ '%'" y="114" fill="black"
                    v-if="occupy.accidentStop<60&&occupy.accidentStop>0">
                {{ occupy.accidentStop + 'min' }}
              </text>
              <!-- 第三个矩形 -->
              <rect :width="occupy.work + '%'" :x="(occupy.plan+occupy.notPlan) +'%'" y="22" height="37"
                    style="fill: rgb(0, 153, 51);stroke-width: 1;stroke: rgb(121, 121, 121);"/>
              <line :x1="occupy.work / 2 + occupy.notPlan + occupy.plan + '%'" y1="59"
                    :x2="occupy.work / 2 + occupy.notPlan + occupy.plan + '%'" y2="75"
                    style="stroke: rgb(99, 99, 99); stroke-width: 2"/>
              <text :x="occupy.work / 2 +occupy.plan +occupy.notPlan-2 + '%'" y="90" fill="black"
                    v-if="occupy.actualWorkTime>0">
                开机时间
              </text>
              <text :x="occupy.work / 2 + occupy.plan +occupy.notPlan-3 + '%'" y="114" fill="black"
                    v-if="parseInt(occupy.actualWorkTime)>1440">
                {{ (occupy.actualWorkTime / 60 / 24).toFixed(0) + '天' + (occupy.actualWorkTime / 60 % 24).toFixed(0) + 'h' + occupy.actualWorkTime % 60 + 'min' }}
              </text>
              <text :x="occupy.work / 2 + occupy.plan +occupy.notPlan-3 + '%'" y="114" fill="black"
                    v-if="occupy.actualWorkTime<1440&&occupy.actualWorkTime>60">
                {{ (occupy.actualWorkTime / 60).toFixed(0) + 'h' + occupy.actualWorkTime % 60 + 'min' }}
              </text>
              <text :x="occupy.work / 2 + occupy.plan +occupy.notPlan-3 + '%'" y="114" fill="black"
                    v-if="occupy.actualWorkTime<60&&occupy.actualWorkTime>0">
                {{ occupy.actualWorkTime + 'min' }}
              </text>
              <!-- 右竖线 -->
              <line x1="100%" y1="0" x2="100%" y2="22" style="stroke: rgb(99, 99, 99); stroke-width: 2"/>
            </svg>
          </div>
        </div>
      </div>
      <div class="flex between">
        <!-- 计划停机时间分析 -->
        <div class="card" style="margin-top:20px;width:49%">
          <div class="card_title " style="width:100%">
            <div style="width:50%">计划停机时间分析</div>
          </div>
          <div class="planAnalysis" style="width:100%" >
            <el-table :data="planAnalysis" style="width: 100%">
              <el-table-column label="序号" prop="num"/>
              <el-table-column label="事件" prop="event"/>
              <el-table-column label="时长" prop="long">
                <div slot-scope="{ row }" v-if="row.long">
                  <div v-if="parseInt(row.long)>1440">
                    {{ (row.long / 60 / 24).toFixed(0) + '天' + (row.long / 60 % 24).toFixed(0) + 'h' + row.long % 60 + 'min' }}
                  </div>
                  <div v-if="row.long<1440&&row.long>60">
                    {{ (row.long / 60).toFixed(0) + 'h' + row.long % 60 + 'min' }}
                  </div>
                  <div v-if="row.long<60">
                    {{ row.long + 'min' }}
                  </div>
                </div>
              </el-table-column>
              <el-table-column label="事件描述" prop="description"/>
              <el-table-column label="操作">
                <div slot-scope="{ row }">
                  <el-button type="text" @click="changeForm(row)">修改</el-button>
                  <el-button type="text" @click="deleteForm(row)">删除</el-button>
                </div>
              </el-table-column>
            </el-table>
            <div class="add" @click="addForm"><i class="el-icon-plus"></i>添加计划停机事件</div>
          </div>
        </div>
        <!-- 产线开关机时刻表 -->
        <div class="card" style="margin-top:20px;width:49%">
          <div  class="card_title flex" style="width:100%">
            <div>产线开关机时刻表</div>
          </div>
          <div class="powerTime" style="width:100%">
            <el-table :data="powerTime" style="width: 100%">
              <el-table-column label="开机时间" prop="powerOn">
                <div slot-scope="{ row }">
                  <div>{{ row.powerOn.slice(0, 10) }}</div>
                  <div>{{ row.powerOn.slice(11, 20) }}</div>
                </div>
              </el-table-column>
              <el-table-column label="关机时间" prop="powerOff">
                <div slot-scope="{ row }">
                  <div>{{ row.powerOff.slice(0, 10) }}</div>
                  <div>{{ row.powerOff.slice(11, 20) }}</div>
                </div>
              </el-table-column>
              <el-table-column label="状态" prop="status">
                <div slot-scope="{ row }">
                  <div class="red">异常</div>
                </div>
              </el-table-column>
              <el-table-column label="事件描述" prop="description">
                <div slot-scope="{ row }">
                  <div v-if="!row.description" class="grey">未知原因</div>
                </div>
              </el-table-column>
              <el-table-column label="操作">
                <div slot-scope="{ row }">
                  <el-button type="text" @click="changePowerTime(row)">修改</el-button>
                  <el-button type="text" @click="deletePowerTime(row)">删除</el-button>
                </div>
              </el-table-column>
            </el-table>
          </div>
        </div>
      </div>
      <!-- 设备AE统计 -->
      <div class="card" style="margin-top:20px">
        <div class="card_title">
          <span>设备AE统计</span>
        </div>
        <div class="aeAnalysis" v-if="aeAnalysis&&aeAnalysis.length>0">
          <el-table :data="aeAnalysis" ref="table1" style="width: 99%">
            <!-- 设备行展开 -->
            <el-table-column type="expand">
              <template slot-scope="props">
                <div style="margin-top:20px" v-if="props.row.equipAccidentReason.length>0" >
                  <div class="wrongAnalysis" style="margin-bottom:20px">
                    <div v-for="(item,index) in props.row.equipAccidentReason" :key="index"
                      style="margin-bottom:20px">
                      <div class="flex between anaName"
                          style="background: rgb(214, 49, 31);height:40px; border-radius: 5px 5px 0 0;">
                        <div class="flex  center">
                          <div class="analysis">{{ item.detail }}</div>
                          <div class="analysis"> 影响程度:{{ item.influence }}</div>
                        </div>
                        <div class="flex center">
                          <div class="analysis1">查看详情</div>
                          <div class="analysis1" @click="changeReason(item)">修改</div>
                          <div class="analysis1" @click="deleteReason(item.id)">删除</div>
                          <!-- <div class="analysis" @click="open"><i :class="showDetail.visible?'el-icon-arrow-down':'el-icon-arrow-right'"></i></div> -->
                        </div>
                      </div>
                      <div>
                        <div class="flex align-center "
                            style="height:50px;border: solid 1px #cccccc; border-radius: 0 0 5px 5px;">
                          <div class="analysisDetail">{{ item.reason }}</div>
                          <div class="analysisDetail"> 开始时间：<span style="font-weight:bold">{{ item.start }}</span></div>
                          <div class="analysisDetail"> 结束时间：<span style="font-weight:bold">{{ item.end }}</span></div>
                          <div class="analysisDetail"> 持续时长:<span
                            style="font-weight:bold">{{ item.long + 'min' }}</span></div>
                        </div>
                      </div>
                    </div> 
                  </div>
                </div>
                <div v-else>
                  <kit-empty>暂无数据</kit-empty>
                </div>
              </template>
            </el-table-column>
            <el-table-column label="设备图片" prop="image">
              <div slot-scope="{ row }" v-if="aeAnalysis.length>0">
                <div v-if="row.equipName.indexOf('无心磨')>=0"><img style="width:50px;height:50px" src='../../assets/device/无心磨.png'/></div>
                <div v-if="row.equipName.indexOf('端面磨')>=0"><img style="width:50px;height:50px" src='../../assets/device/端面磨.png'/></div>
                <div v-if="row.equipName.indexOf('清洗机')>=0"><img style="width:50px;height:50px" src='../../assets/device/清洗机.png'/></div>
                <div v-if="row.equipName.indexOf('检测机')>=0"><img style="width:50px;height:50px" src='../../assets/device/检测机.png'/></div>
                <div v-if="row.equipName.indexOf('上板机')>=0"><img style="width:50px;height:50px" src='../../assets/device/上板机.png'/></div>
                <div v-if="row.equipName.indexOf('印刷机')>=0"><img style="width:50px;height:50px" src='../../assets/device/全自动印刷机.png'/></div>
                <div v-if="row.equipName.indexOf('高速贴装机')>=0"><img style="width:50px;height:50px" src='../../assets/device/高速贴装机.png'/></div>
                <div v-if="row.equipName.indexOf('多功能贴装机')>=0"><img style="width:50px;height:50px" src='../../assets/device/多功能贴装机.png'/></div>
                <div v-if="row.equipName.indexOf('热风')>=0"><img style="width:50px;height:50px" src='../../assets/device/热风.png'/></div>
                <div v-if="row.equipName.indexOf('自动下板机')>=0"><img style="width:50px;height:50px" src='../../assets/device/自动下板机.png'/></div>
              </div>
            </el-table-column>
            <el-table-column label="设备名称" prop="equipName"/>
            <el-table-column label="负荷时间" prop="workTime">
              <div slot-scope="{ row }" v-if="aeAnalysis.length>0">
                <div v-if="parseInt(row.workTime)>1440">
                  {{ (row.workTime / 60 / 24).toFixed(0) + '天' + (row.workTime / 60 % 24).toFixed(0) + 'h' + row.workTime % 60 + 'min' }}
                </div>
                <div v-if="row.workTime<1440&&row.workTime>60">
                  {{ (row.workTime / 60).toFixed(0) + 'h' + row.workTime % 60 + 'min' }}
                </div>
                <div v-if="row.workTime<60">
                  {{ row.workTime + 'min' }}
                </div>
              </div>
            </el-table-column>
            <el-table-column label="开动时间" prop="actualWorkTime">
              <div slot-scope="{ row }">
                <div v-if="parseInt(row.actualWorkTime)>1440">
                  {{ (row.actualWorkTime / 60 / 24).toFixed(0) + '天' + (row.actualWorkTime / 60 % 24).toFixed(0) + 'h' + row.actualWorkTime % 60 + 'min' }}
                </div>
                <div v-if="row.actualWorkTime<1440&&row.actualWorkTime>60">
                  {{ (row.actualWorkTime / 60).toFixed(0) + 'h' + row.actualWorkTime % 60 + 'min' }}
                </div>
                <div v-if="row.actualWorkTime<60">
                  {{ row.actualWorkTime + 'min' }}
                </div>
              </div>
            </el-table-column>
            <el-table-column label="非计划停机时间">
              <div slot-scope="{ row }" style="color:red">
                <div v-if="parseInt(row.accidentStop)>1440">
                  {{ (row.accidentStop / 60 / 24).toFixed(0) + '天' + (row.accidentStop / 60 % 24).toFixed(0) + 'h' + row.accidentStop % 60 + 'min' }}
                </div>
                <div v-if="row.accidentStop<1440&&row.accidentStop>60">
                  {{ (row.accidentStop / 60).toFixed(0) + 'h' + row.accidentStop % 60 + 'min' }}
                </div>
                <div v-if="row.accidentStop<60">
                  {{ row.accidentStop + 'min' }}
                </div>
              </div>
            </el-table-column>
            <el-table-column label="AE">
              <div slot-scope="{ row }">
                {{ row.ae }}
              </div>
            </el-table-column>
            <el-table-column label="操作">
              <div slot-scope="{row}" class="flex row align-center">
                <el-button type="text" size="mini" @click="toggle1(row)">查看详情</el-button>
                <el-button type="text" size="mini" @click="addReason(row)">添加原因</el-button>
              </div>
            </el-table-column>
          </el-table>
        </div>
      </div>
      <!-- 非计划停机时间分析 -->
      <div class="card" style="margin-top:20px">
        <div class="card_title">
          <span>非计划停机时间分析<el-tag style="margin-left:10px">整线（占OEE损失的百分比）</el-tag></span>
        </div>
        <div class="notPlantAnalysis">
          <v-chart
            class="chart"
            :autoresize="true"
            :options="option2.value"
          ></v-chart>
        </div>
      </div>
      <!-- 原因分析 -->
      <div class="card" style="margin-top:20px">
        <div  class="card_title flex between center">
          <div>原因分析
            <el-tag style="margin-left:10px">{{ deviceName }}</el-tag>
          </div>
          <div>
            <el-button plain icon="el-icon-plus" @click="addReason">添加原因</el-button>
          </div>
        </div>
        <div class="wrongAnalysis" style="margin-bottom:20px" v-if="showDetail">
          <div v-for="(item,index) in showDetail.slice(0,num)" :key="index" style="margin-bottom:20px">
            <div class="flex between anaName" style="background: rgb(214, 49, 31);height:40px; border-radius: 5px 5px 0 0;">
              <div class="flex  center">
                <div class="analysis">{{ item.detail }}</div>
                <div class="analysis"> 影响程度:{{ item.influence }}</div>
              </div>
              <div class="flex center">
                <div class="analysis1">查看详情</div>
                <div class="analysis1" @click="changeReason(item)">修改</div>
                <div class="analysis1" @click="deleteReason(item.id)">删除</div>
              </div>
            </div>
            <div>
              <div class="flex align-center " style="height:50px;border: solid 1px #cccccc; border-radius: 0 0 5px 5px;">
                <div class="analysisDetail">{{ item.reason }}</div>
                <div class="analysisDetail"> 开始时间：<span style="font-weight:bold">{{ item.start }}</span></div>
                <div class="analysisDetail"> 结束时间：<span style="font-weight:bold">{{ item.end }}</span></div>
                <div class="analysisDetail"> 持续时长:<span style="font-weight:bold">{{ item.long + 'min' }}</span></div>
              </div>
            </div>
          </div>
        </div>
        <div @click="showMore" class="expand"><i :class="txt=='显示全部'?'el-icon-caret-bottom':'el-icon-caret-top'"></i>{{ txt }}
        </div>
        <!-- 添加计划停机弹框 -->
        <kit-dialog-simple
          v-if="eventForm.visible"
          :modal="eventForm"
          :confirm="eventUpdate"
          width="400px"
        >
          <div slot="title">添加计划停机事件</div>
          <el-form
            v-if="eventForm"
            ref="formStop"
            :model="eventForm"
            label-width="100px"
            label-position="left"
            style="margin: 0 10px"
          >
            <el-form-item label="序号:">
              <div>
                <el-input v-model="eventForm.num" placeholder="请输入序号"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="事件:" prop="event">
              <div>
                <el-input v-model="eventForm.event" :rules="{required: true, message: '请输入事件', trigger: 'blur'}"
                          placeholder="请输入事件"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="事件描述:" prop="description">
              <div>
                <el-input v-model="eventForm.description" placeholder="请输入事件描述"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="开始时间:" prop="dateStart" :rules="{required: true, message: '请选择开始时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="eventForm.dateStart"
                  type="datetime"
                  placeholder="选择日期时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="结束时间:" prop="dateEnd" :rules="{required: true, message: '请选择结束时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="eventForm.dateEnd"
                  type="datetime"
                  placeholder="选择日期时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="时长:">
              <div>
                <el-input v-model="timeDifference1" :disabled="true"></el-input>
              </div>
            </el-form-item>
          </el-form>
        </kit-dialog-simple>
        <!-- 修改计划停机弹框 -->
        <kit-dialog-simple
          v-if="changeEventForm.visible"
          :modal="changeEventForm"
          :confirm="changeEventUpdate"
          width="400px"
        >
          <div slot="title">修改计划停机事件</div>
          <el-form
            v-if="changeEventForm"
            ref="formStopChange"
            :model="changeEventForm"
            label-width="100px"
            label-position="left"
            style="margin: 0 10px"
          >
            <el-form-item label="序号:">
              <div>
                <el-input v-model="changeEventForm.num" placeholder="请输入序号"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="事件:">
              <div>
                <el-input v-model="changeEventForm.event" placeholder="请输入事件"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="事件描述:">
              <div>
                <el-input v-model="changeEventForm.description" placeholder="请输入事件描述"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="开始时间:" prop="dateStart" :rules="{required: true, message: '请选择开始时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="changeEventForm.dateStart"
                  type="datetime"
                  placeholder="选择日期时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="结束时间:" prop="dateEnd" :rules="{required: true, message: '请选择结束时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="changeEventForm.dateEnd"
                  type="datetime"
                  placeholder="选择日期时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="时长:" prop="long">
              <div>
                <el-input v-model="timeDifference2" :disabled="true"></el-input>
              </div>
            </el-form-item>
          </el-form>
        </kit-dialog-simple>
        <!-- 删除计划停机弹框 -->
        <kit-dialog-simple
          :modal="deleteEvent"
          :confirm="deleteEventUpdate"
          width="400px"
        >
          <div slot="title">提示</div>
          <div>此操作将永久删除, 是否继续?</div>
        </kit-dialog-simple>
        <!-- 修改产线开关机信息弹框 -->
        <kit-dialog-simple
          :modal="changePowerTime1"
          :confirm="changePowerTimeUpdate"
          width="400px"
        >
          <div slot="title">修改产线开关机信息</div>
          <el-form
            v-if="changePowerTime1"
            ref="formPowerTime"
            :model="changePowerTime1"
            label-width="100px"
            label-position="left"
            style="margin: 0 10px"
          >
            <el-form-item label="开机时间:" prop="on" :rules="{required: true, message: '请选择开机时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="changePowerTime1.on"
                  type="datetime"
                  placeholder="选择时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="关机时间:" prop="off" :rules="{required: true, message: '请选择关机时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="changePowerTime1.off"
                  type="datetime"
                  placeholder="选择时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="状态:">
              <div>
                <el-input v-model="changePowerTime1.status"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="事件描述:">
              <div>
                <el-input v-model="changePowerTime1.description" placeholder="请输入事件描述"></el-input>
              </div>
            </el-form-item>
          </el-form>
        </kit-dialog-simple>
        <!-- 添加原因弹框 -->
        <kit-dialog-simple
          v-if="reasonForm.visible"
          :modal="reasonForm"
          :confirm="reasonUpdate"
          width="400px"
        >
          <div slot="title">添加原因分析</div>
          <el-form
            v-if="reasonForm"
            ref="formReason"
            :model="reasonForm"
            label-width="100px"
            label-position="left"
            style="margin: 0 10px"
          >
            <el-form-item label="设备选择:">
              <div>
                <el-select v-model="equipName" placeholder="请选择设备">
                  <el-option
                    v-for="item in reasonForm.options"
                    :key="item.name"
                    :label="item.name"
                    :value="item.name">
                  </el-option>
                </el-select>
              </div>
            </el-form-item>
            <el-form-item label="分析描述:">
              <div>
                <el-input v-model="reasonForm.detail" placeholder="请输入事件"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="原因描述:">
              <div>
                <el-input v-model="reasonForm.reason" placeholder="请输入事件"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="开始时间:" prop="dateStart" :rules="{required: true, message: '请选择开始时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="reasonForm.dateStart"
                  type="datetime"
                  placeholder="选择日期时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="结束时间:" prop="dateEnd" :rules="{required: true, message: '请选择结束时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="reasonForm.dateEnd"
                  type="datetime"
                  placeholder="选择日期时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="时长:">
              <div>
                <el-input v-model="timeDifference" :disabled='true'></el-input>
              </div>
            </el-form-item>
          </el-form>
        </kit-dialog-simple>
        <!-- 修改原因弹框 -->
        <kit-dialog-simple
          :modal="reasonChangeForm"
          :confirm="reasonChangeUpdate"
          width="400px"
        >
          <div slot="title">修改原因分析</div>
          <el-form
            v-if="reasonChangeForm"
            ref="formChangeReason"
            :model="reasonChangeForm"
            label-width="100px"
            label-position="left"
            style="margin: 0 10px"
          >
            <el-form-item label="分析描述:">
              <div>
                <el-input v-model="reasonChangeForm.detail" placeholder="请输入事件"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="原因描述:">
              <div>
                <el-input v-model="reasonChangeForm.reason" placeholder="请输入事件"></el-input>
              </div>
            </el-form-item>
            <el-form-item label="开始时间:" prop="dateStart" :rules="{required: true, message: '请选择开始时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="reasonChangeForm.dateStart"
                  type="datetime"
                  placeholder="选择日期时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="结束时间:" prop="dateEnd" :rules="{required: true, message: '请选择结束时间', trigger: 'blur'}">
              <div>
                <el-date-picker
                  v-model="reasonChangeForm.dateEnd"
                  type="datetime"
                  placeholder="选择日期时间">
                </el-date-picker>
              </div>
            </el-form-item>
            <el-form-item label="时长:">
              <div>
                <el-input v-model="timeDifference3" :disabled='true'></el-input>
              </div>
            </el-form-item>
          </el-form>
        </kit-dialog-simple>
        <!-- 删除原因弹框 -->
        <kit-dialog-simple
          :modal="deleteEvent1"
          :confirm="deleteReasonUpdate"
          width="400px"
        >
          <div slot="title">提示</div>
          <div>此操作将永久删除, 是否继续?</div>
        </kit-dialog-simple>
      </div>
    </div>
  </div>
</template>
<script lang='ts'>
import {ref, Ref, onMounted, computed} from "@vue/composition-api";
import {useLoading} from "web-toolkit/src/service/use-decrator";
import {Message} from "element-ui";
import {formatDateTime} from 'web-toolkit/src/utils';
import {ElForm} from 'element-ui/types/form';
import {ElTable} from 'element-ui/types/table';
import {AeList, AeListEquip} from '@/dao/ae';
import {DeviceList} from '@/dao/device';
import {
  StopPlanAdd,
  StopAccidentAdd,
  StopAccidentDelete,
  StopAccidentUpdate,
  StopPlanDelete,
  StopPlanUpdate
} from '@/dao/stop';
export default {
  setup(props: any, ctx: any) {
    const loading = ref(false);
    const deviceName = ref<any>('整线')
    const equipName = ref<any>()
    const equipId = ref<any>()
    const time1 = ref<any>()
    const time2 = ref<any>()
    const table1 = ref<any>()
    const class1 = ref<any>()
    const class2 = ref<any>()
    const option2 = ref<any>()
    const number = ref<any>({});
    const deleteEvent = ref<any>({
      visible: false,
      id: ''
    });
    const deleteEvent1 = ref<any>({
      visible: false,
      id: ''
    });
    const occupy = ref<any>({
      plan: 10,
      notPlan: 20,
      work: 70,
      workTime: '',
      actualWorkTime: '',
      accidentStop: '',
      planStop: '',
    });
    const powerTime = ref<any>([
      {
        on: '',
        off: "",
        status: "",
        description: "",
      },
    ]);
    const planAnalysis = ref<any>([
      {
        num: '',
        event: "",
        long: "",
        description: "",
      },
    ]);
    const noPlanAnalysis = ref<any>(
      {
        total: '',
        powerOff: "",
        equipWrong: "",
        equipAdjust: "",
      },
    );
    const aeAnalysis = ref<any>([
      {
        image: 'https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=3297478543,2018619678&fm=26&gp=0.jpg',
        equipName: "无心磨床1",
        workTime: "5min",
        actualWorkTime: "10min",
        accidentStop: "10min",
      },
    ]);
    const showDetail = ref<any>({
      visible: false,
      detail: '',
      influence: '',
      type: "close",
      start: "",
      end: "",
      long: "",
      reason: "",
    });
    const eventForm = ref<any>({
      visible: false,
      num: "",
      event: "",
      description: "",
      dateStart: "",
      dateEnd: "",
    });
    const reasonForm = ref<any>({
      visible: false,
      options: [],
      detail: "",
      reason: "",
      dateStart: '',
      dateEnd: '',
    });
    const changeEventForm = ref<any>({
      visible: false,
      detail: "",
      reason: "",
      dateStart: "",
      dateEnd: "",
      id: ""
    });
    const changePowerTime1 = ref<any>({
      visible: false,
      on: "",
      off: "",
      description: "",
      status: "",
    });
    const reasonChangeForm = ref<any>({
      visible: false,
      num: "",
      event: "",
      description: "",
      dateStart: "",
      dateEnd: "",
      id: ""
    });
    const formReason = ref<ElForm | null>(null);
    const formStop = ref<ElForm | null>(null);
    const formStopChange = ref<ElForm | null>(null);
    const formChangeReason = ref<ElForm | null>(null);
    const formPowerTime = ref<ElForm | null>(null);
    // 转换时间
    const timeDifference = computed(() => {
      if (!reasonForm.value.dateStart || !reasonForm.value.dateEnd) {
        return ''
      }
      const time = reasonForm.value.dateEnd.getTime() - reasonForm.value.dateStart.getTime();
      const H = parseInt((time / 1000 / 60 / 60).toFixed(0))
      const Min = parseInt((time / 1000 / 60 % 60).toFixed(0))
      const S = time / 1000 % 60
      if (time < 0) {
        return ''
      } else if (H == 0 && Min == 0) {
        return S + '秒'
      } else if (H == 0) {
        return Min + '分钟' + S + '秒'
      } else {
        return H + '小时' + Min + '分钟' + S + '秒'
      }
    });
    const timeDifference1 = computed(() => {
      if (!eventForm.value.dateStart || !eventForm.value.dateEnd) {
        return ''
      }
      const time = eventForm.value.dateEnd.getTime() - eventForm.value.dateStart.getTime();
      const H = parseInt((time / 1000 / 60 / 60).toFixed(0))
      const Min = parseInt((time / 1000 / 60 % 60).toFixed(0))
      const S = time / 1000 % 60
      if (time < 0) {
        return ''
      } else if (H == 0 && Min == 0) {
        return S + '秒'
      } else if (H == 0) {
        return Min + '分钟' + S + '秒'
      } else {
        return H + '小时' + Min + '分钟' + S + '秒'
      }
    });
    const timeDifference2 = computed(() => {
      if (!changeEventForm.value.dateStart || !changeEventForm.value.dateEnd) {
        return ''
      }
      const time = changeEventForm.value.dateEnd.getTime() - changeEventForm.value.dateStart.getTime();
      const H = parseInt((time / 1000 / 60 / 60).toFixed(0))
      const Min = parseInt((time / 1000 / 60 % 60).toFixed(0))
      const S = time / 1000 % 60
      if (time < 0) {
        return ''
      } else if (H == 0 && Min == 0) {
        return S + '秒'
      } else if (H == 0) {
        return Min + '分钟' + S + '秒'
      } else {
        return H + '小时' + Min + '分钟' + S + '秒'
      }
    });
    const timeDifference3 = computed(() => {
      if (!reasonChangeForm.value.dateStart || !reasonChangeForm.value.dateEnd) {
        return ''
      }
      const time = reasonChangeForm.value.dateEnd.getTime() - reasonChangeForm.value.dateStart.getTime();
      const H = parseInt((time / 1000 / 60 / 60).toFixed(0))
      const Min = parseInt((time / 1000 / 60 % 60).toFixed(0))
      const S = time / 1000 % 60
      if (time < 0) {
        return ''
      } else if (H == 0 && Min == 0) {
        return S + '秒'
      } else if (H == 0) {
        return Min + '分钟' + S + '秒'
      } else {
        return H + '小时' + Min + '分钟' + S + '秒'
      }
    });
    
    // 收起
    const expand = ref<any>({
      show: true,
      txt: '收起'
    })

    function expandMore() {
      expand.value.show = !expand.value.show;
      if (expand.value.show) {
        expand.value.txt = '收起'
      } else {
        expand.value.txt = '显示'
      }
    }

    // 下拉刷新
    const txt = ref<any>('查看全部')
    const num = ref<any>(3)
    const isShow = ref(false);
    function showMore() {
      isShow.value = !isShow.value;
      if (isShow.value) {
        num.value = showDetail.value.length;
        txt.value = '收起'
      } else {
        num.value = 3;
        txt.value = '显示全部'
      }
    }
    //  下拉查看详情
    async function toggle1(row: any) {
      (table1.value as ElTable).toggleRowExpansion(row);
    }
    function addForm() {
      eventForm.value = {
        visible: true,
        num: "",
        event: "",
        description: "",
        dateStart: "",
        dateEnd: "",
      }
    }
    function changeForm(row: any) {
      changeEventForm.value.visible = true;
      changeEventForm.value.id = row.id;
      changeEventForm.value.num = row.num;
      changeEventForm.value.event = row.event;
      changeEventForm.value.description = row.description;
    }
    function changePowerTime(row: any) {
      changePowerTime1.value.visible = true;
      changePowerTime1.value.on = row.powerOn;
      changePowerTime1.value.off = row.powerOff;
      changePowerTime1.value.status = '异常';
      changePowerTime1.value.description = row.description;
    }
    // 原因增添改
    async function addReason(row: any) {
      reasonForm.value = {
        visible: true,
        options: [],
        detail: "",
        reason: "",
        dateStart: '',
        dateEnd: '',
      }
      if (row.equipName) {
        equipName.value = row.equipName
      } else {
        equipName.value = ''
        let data = await DeviceList()
        reasonForm.value.options = data
      }
    }
    async function deleteReason(data: any) {
      deleteEvent1.value.visible = true;
      deleteEvent1.value.id = data;
    }
    async function deleteReasonUpdate() {
      await StopAccidentDelete({
        id: deleteEvent1.value.id
      })
      await query1()
      Message.success("删除成功");
      deleteEvent1.value.visible = false;
    }
    async function changeReason(row: any) {
      reasonChangeForm.value.visible = true;
      reasonChangeForm.value.id = row.id;
      reasonChangeForm.value.detail = row.detail;
      reasonChangeForm.value.reason = row.reason;
    }
    // 计划停机删除
    async function deleteForm(row: any) {
      deleteEvent.value.visible = true;
      deleteEvent.value.id = row.id;
    }
    async function deleteEventUpdate() {
      await StopPlanDelete({
        id: deleteEvent.value.id
      })
      await query1()
      Message.success("删除成功");
      deleteEvent.value.visible = false;
    }
    async function reasonUpdate() {
      const valid = await (formReason.value as ElForm).validate();
      if (!valid) {
        return;
      }
      if (reasonForm.value.dateStart > reasonForm.value.dateEnd) {
        reasonForm.value.visible = true;
        Message.warning("结束时间不能小于开始时间");
      } else {
        let data = await DeviceList()
        equipId.value = data.filter((item: any) => item.name == equipName.value)[0].id
        await StopAccidentAdd({
          detail: reasonForm.value.detail,
          startTime: reasonForm.value.dateStart,
          endTime: reasonForm.value.dateEnd,
          reason: reasonForm.value.reason,
          equipmentId: equipId.value,
          equipmentName: equipName.value
        })
        await query1()
        Message.success("添加成功");
        reasonForm.value.visible = false;
      }
    }
    async function reasonChangeUpdate() {
      const valid = await (formChangeReason.value as ElForm).validate();
      if (!valid) {
        return;
      }
      if (reasonChangeForm.value.dateStart > reasonChangeForm.value.dateEnd) {
        reasonChangeForm.value.visible = true;
        Message.warning("结束时间不能小于开始时间");
      } else {
        await StopAccidentUpdate({
          detail: reasonChangeForm.value.detail,
          startTime: reasonChangeForm.value.dateStart,
          endTime: reasonChangeForm.value.dateEnd,
          reason: reasonChangeForm.value.reason,
          id: reasonChangeForm.value.id
        })
        Message.success("修改成功");
        await query1()
        reasonChangeForm.value.visible = false;
      }
    }
    async function eventUpdate() {
      const valid = await (formStop.value as ElForm).validate();
      if (!valid) {
        return;
      }
      if (eventForm.value.dateStart > eventForm.value.dateEnd) {
        eventForm.value.visible = true;
        Message.warning("结束时间不能小于开始时间");
      } else {
        let a = await StopPlanAdd({
          event: eventForm.value.event,
          startTime: eventForm.value.dateStart,
          endTime: eventForm.value.dateEnd,
          description: eventForm.value.description,
        })
        Message.success("添加成功");
        await query1()
        eventForm.value.visible = false;
      }
    }
    async function changeEventUpdate(row: any) {
      const valid = await (formStopChange.value as ElForm).validate();
      if (!valid) {
        return;
      }
      if (changeEventForm.value.dateStart > changeEventForm.value.dateEnd) {
        changeEventForm.value.visible = true;
        Message.warning("结束时间不能小于开始时间");
      } else {
        await StopPlanUpdate({
          event: changeEventForm.value.event,
          startTime: changeEventForm.value.dateStart,
          endTime: changeEventForm.value.dateEnd,
          description: changeEventForm.value.description,
          id: changeEventForm.value.id
        })
        Message.success("修改成功");
        await query1()
        changeEventForm.value.visible = false;
      }
    }
    async function changePowerTimeUpdate(row: any) {
      const valid = await (formPowerTime.value as ElForm).validate();
      if (!valid) {
        return;
      }
      if (changePowerTime1.value.on > changePowerTime1.value.off) {
        changePowerTime1.value.visible = true;
        Message.warning("关机时间不能小于开机时间");
      } else {
        Message.success("修改成功");
        await query1()
        changePowerTime1.value.visible = false;
      }
    }
    async function query1() {
      let data = await AeList({
        timeStart: time1.value,
        timeEnd: time2.value,
        timeStartShift: class1.value,
        timeEndShift: class2.value
      })
      // 时间轴
      number.value = data.allLineAe
      const alltime = parseFloat(number.value.planStop + number.value.actualWorkTime + number.value.accidentStop)
      occupy.value.plan = parseFloat(((number.value.planStop / alltime) * 100).toFixed(2))
      occupy.value.notPlan = parseFloat(((number.value.accidentStop / alltime) * 100).toFixed(2))
      occupy.value.work = parseFloat(((number.value.actualWorkTime / alltime) * 100).toFixed(2))
      occupy.value.actualWorkTime = number.value.actualWorkTime
      occupy.value.workTime = number.value.workTime
      occupy.value.accidentStop = number.value.accidentStop
      occupy.value.planStop = number.value.planStop
      // 设备AE分析
      aeAnalysis.value = data.equipAe
      console.log(aeAnalysis.value)
      // 计划停机时间分析
      planAnalysis.value = data.planStopAnalysis
      // 产线开关机时刻表
      powerTime.value = data.powerOnOff
      // 非计划停机时间分析
      noPlanAnalysis.value = data.accidentStopAnalysis
      option2.value = ref<any>({
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow",
          },
        },
        grid: {
          left: "3%",
          right: "4%",
          bottom: "3%",
          top:0,
          containLabel: true,
        },
        xAxis: {
          type: "value",
          name: "百分比",
          boundaryGap: [0, 0.01],
        },
        yAxis: {
          type: "category",
          data: ["未开机", "设备故障", "设备调整", "人员调整"],
        },
        color: ["#61A5E8"],
        series: [
          {
            name: "所占百分比",
            type: "bar",
            data: [noPlanAnalysis.value.total, noPlanAnalysis.value.equipWrong, noPlanAnalysis.value.equipAdjust, noPlanAnalysis.value.powerOff],
            itemStyle: {
              //上方显示数值
              normal: {
                label: {
                  show: true, //开启显示
                  position: "right", //在y右方显示
                  formatter: function (v: any) {
                    var val = v.data;
                    return val+'%';
                  },
                  textStyle: {
                    //数值样式
                    color: "black",
                    fontSize: 16,
                  },
                  color: "#61A5E8",
                },
              },
            },
          },
        ],
      });
      // 原因分析
      showDetail.value = data.accidentReasonAnalysis
      // 名称改变
      deviceName.value = '整线'
      let arr12=[{id:213,name:'21312'},{id:213,name:'21312'}]
      let arr34=arr12.map((item:any)=>(item.id,item.name))
      console.log(arr34)
    }
    async function query(time11: any, time22: any, class11: any, class22: any) {
      //index页面的query函数之后执行对应页面的query函数，aepeqe页面的query函数接受index传过来
      //的参数，比如时间timerange或者班次
      time1.value = formatDateTime(time11)
      time2.value = formatDateTime(time22)
      class1.value = class11
      class2.value = class22
      await query1()
    }
    onMounted(useLoading(loading, async function () {
      query1()
    }));
    return {
      loading,
      number, txt, num, isShow, showMore, deleteEventUpdate, deleteReasonUpdate,
      occupy, changePowerTimeUpdate, formPowerTime, deleteEvent, deleteEvent1,
      planAnalysis, noPlanAnalysis, powerTime, changePowerTime, 
      showDetail, equipName, equipId, table1, toggle1, changePowerTime1,
      option2, eventForm, addForm, eventUpdate, changeEventForm, changeForm,
      aeAnalysis, changeEventUpdate, deleteForm, reasonForm, addReason, reasonUpdate,
      deviceName, timeDifference, timeDifference1, timeDifference2, formReason, formStop, formStopChange,
      query: useLoading(loading, query), time1, time2, class1, class2,
      query1, deleteReason, changeReason, reasonChangeForm, timeDifference3, formChangeReason, reasonChangeUpdate
    };
  },
};
</script>
<style lang="scss" scoped>
.all {
  margin-top: 15px;
  min-height: 1000px;
  .title {
    height: 150px;
    padding-top: 10px;
    .pe {
      border: 1px solid #e9e9e9;
      width: 9%;
      height: 104px;
      font-weight: bold;
      background: #e3effe;
      .title1 {
        font-size: 14px;
      }
      .number {
        font-size: 16px;
        margin-top: 10px;
      }
    }
    .time {
      width: 90%;
      padding: 10px;
    }
  }
  .planAnalysis {
    .add:hover {
      border: 1px rgb(97, 165, 232) dashed;
      color: rgb(97, 165, 232)
    }
  ;
    .add {
      width: 100%;
      height: 30px;
      line-height: 30px;
      border: 1px grey dashed;
      text-align: center;
      color: grey;
      cursor: pointer;
    }
  }
  .powerTime {
    .red {
      color: red
    }
    .grey {
      color: gray
    }
  }
  .aeAnalysis {
    .init {
      color: grey;
      cursor: pointer;
    }
    .change {
      color: rgb(97, 165, 232);
      cursor: pointer;
    }
  }
  .notPlantAnalysis {
    height: 300px;
    margin-top: 10px;
    .chart {
      width: 100%;
      height: 90%;
    }
  }
  .wrongAnalysis {
    .analysis {
      margin: 0 10px;
    }
    .analysis1 {
      margin: 0 10px;
      cursor: pointer;
    }
    .analysis1:hover {
      color: rgb(97, 165, 232)
    }
  ;
    .analysisDetail {
      margin-left: 10px;
      color: grey;
    }
  }
  .expand {
    width: 100%;
    height: 30px;
    line-height: 30px;
    // border:1px grey solid;
    box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04);
    text-align: center;
    color: grey;
    cursor: pointer;
  }
  .expand:hover {
    border: 1px rgb(97, 165, 232) dashed;
    color: rgb(97, 165, 232)
  }
;
}
</style>
