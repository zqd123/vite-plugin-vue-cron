<template>
  <el-form>
    <el-form-item>
      <el-radio v-model="radioValue" :label="1">
        周，允许的通配符[, - * ? / L #]
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="2"> 不指定 </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="3">
        周期从星期
        <el-select clearable v-model="cycle01">
          <el-option
            v-for="(item, index) of weekList"
            :key="index"
            :label="item.value"
            :value="item.key"
            :disabled="item.key === 1"
            >{{ item.value }}</el-option
          >
        </el-select>
        -
        <el-select clearable v-model="cycle02">
          <el-option
            v-for="(item, index) of weekList"
            :key="index"
            :label="item.value"
            :value="item.key"
            :disabled="item.key < cycle01 && item.key !== 1"
            >{{ item.value }}</el-option
          >
        </el-select>
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="4">
        第
        <el-input-number v-model="average01" :min="1" :max="4" /> 周的星期
        <el-select clearable v-model="average02">
          <el-option
            v-for="(item, index) of weekList"
            :key="index"
            :label="item.value"
            :value="item.key"
            >{{ item.value }}</el-option
          >
        </el-select>
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="5">
        本月最后一个星期
        <el-select clearable v-model="weekday">
          <el-option
            v-for="(item, index) of weekList"
            :key="index"
            :label="item.value"
            :value="item.key"
            >{{ item.value }}</el-option
          >
        </el-select>
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="6">
        指定
        <el-select
          clearable
          v-model="checkboxList"
          placeholder="可多选"
          multiple
          style="width: 100%"
        >
          <el-option
            v-for="(item, index) of weekList"
            :key="index"
            :label="item.value"
            :value="String(item.key)"
            >{{ item.value }}</el-option
          >
        </el-select>
      </el-radio>
    </el-form-item>
  </el-form>
</template>

<script setup lang="ts">
import { computed, ref, watch } from "vue";
//=======================** emit **===================================//
const emit = defineEmits(["update"]);
//=======================** props **===================================//
const props = defineProps(["check", "cron"]);
//=======================** data **===================================//
/**radio */
const radioValue = ref(2);
/**本月最后一个星期 */
const weekday = ref(2);
/**周期1 */
const cycle01 = ref(1);
const cycle02 = ref(2);
/**周期2 */
const average01 = ref(0);
const average02 = ref(1);
/**指定 */
const checkboxList = ref([]);
/**星期列表 */
const weekList = ref([
  {
    key: 2,
    value: "星期一",
  },
  {
    key: 3,
    value: "星期二",
  },
  {
    key: 4,
    value: "星期三",
  },
  {
    key: 5,
    value: "星期四",
  },
  {
    key: 6,
    value: "星期五",
  },
  {
    key: 7,
    value: "星期六",
  },
  {
    key: 1,
    value: "星期日",
  },
]);
const checkNum = props.check;
//=======================** computed **===================================//
// 计算两个周期值
const cycleTotal = computed(() => {
  cycle01.value = checkNum(cycle01.value, 1, 7);
  cycle02.value = checkNum(cycle02.value, 1, 7);
  return cycle01.value + "-" + cycle02.value;
});
// 计算平均用到的值
const averageTotal = computed(() => {
  average01.value = checkNum(average01.value, 1, 4);
  average02.value = checkNum(average02.value, 1, 7);
  return average02.value + "#" + average01.value;
});
// 最近的工作日（格式）
const weekdayCheck = computed(() => {
  weekday.value = checkNum(weekday.value, 1, 7);
  return weekday.value;
});
// 计算勾选的checkbox值合集
const checkboxString = computed(() => {
  let str = checkboxList.value.join();
  return str == "" ? "*" : str;
});

//=======================** methods **===================================//
// 单选按钮值变化时
const radioChange = () => {
  if (radioValue.value !== 2 && props.cron.day !== "?") {
    emit("update", "day", "?", "week");
  }
  switch (radioValue.value) {
    case 1:
      emit("update", "week", "*");
      break;
    case 2:
      emit("update", "week", "?");
      break;
    case 3:
      emit("update", "week", cycleTotal.value);
      break;
    case 4:
      emit("update", "week", averageTotal.value);
      break;
    case 5:
      emit("update", "week", weekdayCheck.value + "L");
      break;
    case 6:
      emit("update", "week", checkboxString.value);
      break;
  }
};

// 周期两个值变化时
const cycleChange = () => {
  if (radioValue.value === 3) {
    emit("update", "week", cycleTotal.value);
  }
};
// 平均两个值变化时
const averageChange = () => {
  if (radioValue.value === 4) {
    emit("update", "week", averageTotal.value);
  }
};
// 最近工作日值变化时
const weekdayChange = () => {
  if (radioValue.value === 5) {
    emit("update", "week", weekday.value + "L");
  }
};
// checkbox值变化时
const checkboxChange = () => {
  if (radioValue.value === 6) {
    emit("update", "week", checkboxString.value);
  }
};
//=======================** watch **===================================//
watch(radioValue, radioChange);
watch(cycleTotal, cycleChange);
watch(averageTotal, averageChange);
watch(weekdayCheck, weekdayChange);
watch(checkboxString, checkboxChange);
//=======================** 导出（父组件访问） **===================================//
defineExpose({
  radioValue,
  cycle01,
  cycle02,
  weekday,
  average01,
  average02,
  checkboxList,
});
</script>
