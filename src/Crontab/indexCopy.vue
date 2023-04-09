<template>
  <div>
    <el-tabs type="border-card">
      <el-tab-pane label="秒" v-if="shouldHide('second')">
        <CrontabSecond
          @update="updateCrontabValue"
          :check="checkNumber"
          :cron="crontabValueObj"
          ref="cronsecond"
        />
      </el-tab-pane>

      <el-tab-pane label="分钟" v-if="shouldHide('min')">
        <CrontabMin
          @update="updateCrontabValue"
          :check="checkNumber"
          :cron="crontabValueObj"
          ref="cronmin"
        />
      </el-tab-pane>

      <el-tab-pane label="小时" v-if="shouldHide('hour')">
        <CrontabHour
          @update="updateCrontabValue"
          :check="checkNumber"
          :cron="crontabValueObj"
          ref="cronhour"
        />
      </el-tab-pane>

      <el-tab-pane label="日" v-if="shouldHide('day')">
        <CrontabDay
          @update="updateCrontabValue"
          :check="checkNumber"
          :cron="crontabValueObj"
          ref="cronday"
        />
      </el-tab-pane>

      <el-tab-pane label="月" v-if="shouldHide('month')">
        <CrontabMonth
          @update="updateCrontabValue"
          :check="checkNumber"
          :cron="crontabValueObj"
          ref="cronmonth"
        />
      </el-tab-pane>

      <el-tab-pane label="周" v-if="shouldHide('week')">
        <CrontabWeek
          @update="updateCrontabValue"
          :check="checkNumber"
          :cron="crontabValueObj"
          ref="cronweek"
        />
      </el-tab-pane>

      <el-tab-pane label="年" v-if="shouldHide('year')">
        <CrontabYear
          @update="updateCrontabValue"
          :check="checkNumber"
          :cron="crontabValueObj"
          ref="cronyear"
        />
      </el-tab-pane>
    </el-tabs>

    <div class="popup-main">
      <div class="popup-result">
        <p class="title">时间表达式</p>
        <table>
          <thead>
            <th v-for="item of tabTitles" width="40" :key="item">{{ item }}</th>
            <th>Cron 表达式</th>
          </thead>
          <tbody>
            <td>
              <span>{{ crontabValueObj.second }}</span>
            </td>
            <td>
              <span>{{ crontabValueObj.min }}</span>
            </td>
            <td>
              <span>{{ crontabValueObj.hour }}</span>
            </td>
            <td>
              <span>{{ crontabValueObj.day }}</span>
            </td>
            <td>
              <span>{{ crontabValueObj.month }}</span>
            </td>
            <td>
              <span>{{ crontabValueObj.week }}</span>
            </td>
            <td>
              <span>{{ crontabValueObj.year }}</span>
            </td>
            <td>
              <span>{{ crontabValueString }}</span>
            </td>
          </tbody>
        </table>
      </div>
      <CrontabResult :ex="crontabValueString"></CrontabResult>

      <div class="pop_btn">
        <el-button type="primary" @click="submitFill">确定</el-button>
        <el-button type="warning" @click="clearCron">重置</el-button>
        <el-button @click="hidePopup">取消</el-button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import CrontabSecond from "./secondCopy.vue";
import CrontabMin from "./minCopy.vue";
import CrontabHour from "./hourCopy.vue";
import CrontabDay from "./dayCopy.vue";
import CrontabMonth from "./monthCopy.vue";
import CrontabWeek from "./weekCopy.vue";
import CrontabYear from "./yearCopy.vue";
import CrontabResult from "./resultCopy.vue";
import { computed, onMounted, ref, watch } from "vue";
enum Names {
  "second",
  "min",
  "hour",
  "day",
  "month",
  "week",
  "year",
}
interface CrontabValueObj {
  second: string;
  min: string;
  hour: string;
  day: string;
  month: string;
  week: string;
  year: string;
}
//=======================** emit **===================================//
const emit = defineEmits(["hide", "fill"]);
//=======================** ref实例 **===================================//
const cronsecond = ref<InstanceType<typeof CrontabSecond> | null>();
const cronmin = ref<InstanceType<typeof CrontabMin> | null>();
const cronhour = ref<InstanceType<typeof CrontabHour> | null>();
const cronday = ref<InstanceType<typeof CrontabDay> | null>();
const cronmonth = ref<InstanceType<typeof CrontabMonth> | null>();
const cronweek = ref<InstanceType<typeof CrontabWeek> | null>();
const cronyear = ref<InstanceType<typeof CrontabResult> | null>();
//=======================** data **===================================//
/**tab的title */
const tabTitles = ["秒", "分钟", "小时", "日", "月", "周", "年"];
/**当前激活的tab */
const tabActive = ref(0);
/**cron对象 */
const crontabValueObj = ref({
  second: "*",
  min: "*",
  hour: "*",
  day: "*",
  month: "*",
  week: "?",
  year: "",
});
//=======================** props **===================================//
/**
 * expression cron表达式
 * hideComponent 隐藏某几个tab（例如：隐藏秒）
 */
const props = defineProps(["expression", "hideComponent"]);
//=======================** computed **===================================//
const crontabValueString = computed(() => {
  let obj = crontabValueObj.value;
  let str =
    obj.second +
    " " +
    obj.min +
    " " +
    obj.hour +
    " " +
    obj.day +
    " " +
    obj.month +
    " " +
    obj.week +
    (obj.year == "" ? "" : " " + obj.year);
  return str;
});
//=======================** methods **===================================//
/**隐藏某tab */
const shouldHide = (key: string) => {
  if (props.hideComponent && props.hideComponent.includes(key)) return false;
  return true;
};
/** 反解析cron表达式*/
const resolveExp = <T extends CrontabValueObj, K extends keyof T>() => {
  if (props.expression) {
    let arr = props.expression.split(" ");
    if (arr.length >= 6) {
      //6 位以上是合法表达式
      let obj = {
        second: arr[0],
        min: arr[1],
        hour: arr[2],
        day: arr[3],
        month: arr[4],
        week: arr[5],
        year: arr[6] ? arr[6] : "",
      };
      crontabValueObj.value = {
        ...obj,
      };
      for (let i in obj) {
        if (obj[i as keyof typeof obj])
          changeRadio(i, obj[i as keyof typeof obj]);
      }
    }
  } else {
    // 没有传入的表达式 则还原
    clearCron();
  }
};
// tab切换值
const tabCheck = (index: number) => {
  tabActive.value = index;
};
// 由子组件触发，更改表达式组成的字段值
const updateCrontabValue = (name: string, value: any, from: any) => {
  // "updateCrontabValue", name, value, from;
  crontabValueObj.value[name as keyof typeof crontabValueObj.value] = value;
  if (from && from !== name) {
    // console.log(`来自组件 ${from} 改变了 ${name} ${value}`);
    changeRadio(name, value);
  }
};

/**赋值到组件 */
const changeRadio = (name: string, value: any) => {
  let arr = ["second", "min", "hour", "month"],
    refName = "cron" + name,
    insValue,
    refInstance: any;
  switch (name) {
    case "second":
      refInstance = cronsecond;
      break;
    case "min":
      refInstance = cronmin;
      break;
    case "hour":
      refInstance = cronhour;
      break;
    case "day":
      refInstance = cronday;
      break;
    case "month":
      refInstance = cronmonth;
      break;
    case "week":
      refInstance = cronweek;
      break;
    case "year":
      refInstance = cronyear;
      break;

    default:
      break;
  }
  if (!refInstance) return;

  if (arr.includes(name)) {
    if (value === "*") {
      insValue = 1;
    } else if (value.indexOf("-") > -1) {
      let indexArr = value.split("-");
      isNaN(indexArr[0])
        ? (refInstance.value.cycle01 = 0)
        : (refInstance.value.cycle01 = indexArr[0]);
      refInstance.value.cycle02 = indexArr[1];
      insValue = 2;
    } else if (value.indexOf("/") > -1) {
      let indexArr = value.split("/");
      isNaN(indexArr[0])
        ? (refInstance.value.average01 = 0)
        : (refInstance.value.average01 = indexArr[0]);
      refInstance.value.average02 = indexArr[1];
      insValue = 3;
    } else {
      insValue = 4;
      refInstance.value.checkboxList = value.split(",");
    }
  } else if (name == "day") {
    if (value === "*") {
      insValue = 1;
    } else if (value == "?") {
      insValue = 2;
    } else if (value.indexOf("-") > -1) {
      let indexArr = value.split("-");
      isNaN(indexArr[0])
        ? (refInstance.value.cycle01 = 0)
        : (refInstance.value.cycle01 = indexArr[0]);
      refInstance.value.cycle02 = indexArr[1];
      insValue = 3;
    } else if (value.indexOf("/") > -1) {
      let indexArr = value.split("/");
      isNaN(indexArr[0])
        ? (refInstance.value.average01 = 0)
        : (refInstance.value.average01 = indexArr[0]);
      refInstance.value.average02 = indexArr[1];
      insValue = 4;
    } else if (value.indexOf("W") > -1) {
      let indexArr = value.split("W");
      isNaN(indexArr[0])
        ? (refInstance.value.workday = 0)
        : (refInstance.value.workday = indexArr[0]);
      insValue = 5;
    } else if (value === "L") {
      insValue = 6;
    } else {
      refInstance.value.checkboxList = value.split(",");
      insValue = 7;
    }
  } else if (name == "week") {
    if (value === "*") {
      insValue = 1;
    } else if (value == "?") {
      insValue = 2;
    } else if (value.indexOf("-") > -1) {
      let indexArr = value.split("-");
      isNaN(indexArr[0])
        ? (refInstance.value.cycle01 = 0)
        : (refInstance.value.cycle01 = indexArr[0]);
      refInstance.value.cycle02 = indexArr[1];
      insValue = 3;
    } else if (value.indexOf("#") > -1) {
      let indexArr = value.split("#");
      isNaN(indexArr[0])
        ? (refInstance.value.average01 = 1)
        : (refInstance.value.average01 = indexArr[0]);
      refInstance.value.average02 = indexArr[1];
      insValue = 4;
    } else if (value.indexOf("L") > -1) {
      let indexArr = value.split("L");
      isNaN(indexArr[0])
        ? (refInstance.value.weekday = 1)
        : (refInstance.value.weekday = indexArr[0]);
      insValue = 5;
    } else {
      refInstance.value.checkboxList = value.split(",");
      insValue = 6;
    }
  } else if (name == "year") {
    if (value == "") {
      insValue = 1;
    } else if (value == "*") {
      insValue = 2;
    } else if (value.indexOf("-") > -1) {
      insValue = 3;
    } else if (value.indexOf("/") > -1) {
      insValue = 4;
    } else {
      refInstance.value.checkboxList = value.split(",");
      insValue = 5;
    }
  }
  refInstance.value.radioValue = insValue;
};
/**表单选项的子组件校验数字格式（通过-props传递） */
const checkNumber = (value: any, minLimit: any, maxLimit: any) => {
  // 检查必须为整数
  value = Math.floor(value);
  if (value < minLimit) {
    value = minLimit;
  } else if (value > maxLimit) {
    value = maxLimit;
  }
  return value;
};
/**隐藏弹窗 */
const hidePopup = () => {
  emit("hide");
};
/**填充表达式 */
const submitFill = () => {
  emit("fill", crontabValueString.value);
  hidePopup();
};

/**还原cron默认值 */
const clearCron = () => {
  // 还原选择项
  ("准备还原");
  crontabValueObj.value = {
    second: "*",
    min: "*",
    hour: "*",
    day: "*",
    month: "*",
    week: "?",
    year: "",
  };
  for (let j in crontabValueObj.value) {
    changeRadio(
      j,
      crontabValueObj.value[j as keyof typeof crontabValueObj.value]
    );
  }
};
//=======================** watch **===================================//
watch(
  () => props.expression,
  () => {
    resolveExp();
  }
);
watch(
  () => props.hideComponent,
  (value) => {
    console.log("value:", value);
  }
);
//=======================** 生命周期 **===================================//
onMounted(() => {
  resolveExp();
});
</script>
<style scoped>
.pop_btn {
  text-align: center;
  margin-top: 20px;
}

.popup-main {
  position: relative;
  margin: 10px auto;
  background: #fff;
  border-radius: 5px;
  font-size: 12px;
  overflow: hidden;
}

.popup-title {
  overflow: hidden;
  line-height: 34px;
  padding-top: 6px;
  background: #f2f2f2;
}

.popup-result {
  box-sizing: border-box;
  line-height: 24px;
  margin: 25px auto;
  padding: 15px 10px 10px;
  border: 1px solid #ccc;
  position: relative;
}

.popup-result .title {
  position: absolute;
  top: -28px;
  left: 50%;
  width: 140px;
  font-size: 14px;
  margin-left: -70px;
  text-align: center;
  line-height: 30px;
  background: #fff;
}

.popup-result table {
  text-align: center;
  width: 100%;
  margin: 0 auto;
}

.popup-result table span {
  display: block;
  width: 100%;
  font-family: arial;
  line-height: 30px;
  height: 30px;
  white-space: nowrap;
  overflow: hidden;
  border: 1px solid #e8e8e8;
}

.popup-result-scroll {
  font-size: 12px;
  line-height: 24px;
  height: 10em;
  overflow-y: auto;
}
</style>
