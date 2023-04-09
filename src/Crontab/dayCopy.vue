<template>
  <el-form>
    <el-form-item>
      <el-radio v-model="radioValue" :label="1">
        日，允许的通配符[, - * ? / L W]
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="2"> 不指定 </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="3">
        周期从
        <el-input-number v-model="cycle01" :min="1" :max="30" /> -
        <el-input-number
          v-model="cycle02"
          :min="cycle01 ? cycle01 + 1 : 2"
          :max="31"
        />
        日
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="4">
        从
        <el-input-number v-model="average01" :min="1" :max="30" /> 号开始，每
        <el-input-number
          v-model="average02"
          :min="1"
          :max="31 - average01 || 1"
        />
        日执行一次
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="5">
        每月
        <el-input-number v-model="workday" :min="1" :max="31" />
        号最近的那个工作日
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="6"> 本月最后一天 </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="7">
        指定
        <el-select
          clearable
          v-model="checkboxList"
          placeholder="可多选"
          multiple
          style="width: 100%"
        >
          <el-option v-for="item in 31" :key="item" :value="item">{{
            item
          }}</el-option>
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
const radioValue = ref(1);
/**工作日 */
const workday = ref(1);
/**周期1 */
const cycle01 = ref(1);
const cycle02 = ref(2);
/**周期2 */
const average01 = ref(0);
const average02 = ref(1);
/**指定 */
const checkboxList = ref([]);
const checkNum = props.check;
//=======================** computed **===================================//
// 计算两个周期值
const cycleTotal = computed(() => {
  const _cycle01 = checkNum(cycle01.value, 1, 30);
  const _cycle02 = checkNum(cycle02.value, _cycle01 ? _cycle01 + 1 : 2, 31, 31);
  return _cycle01 + "-" + _cycle02;
});
// 计算平均用到的值
const averageTotal = computed(() => {
  const _average01 = checkNum(average01.value, 1, 30);
  const _average02 = checkNum(average02.value, 1, 31 - _average01 || 0);
  return _average01 + "/" + _average02;
});
// 计算工作日格式
const workdayCheck = computed(() => {
  const _workday = checkNum(workday.value, 1, 31);
  return _workday;
});
// 计算勾选的checkbox值合集
const checkboxString = computed(() => {
  let str = checkboxList.value.join();
  return str == "" ? "*" : str;
});

//=======================** methods **===================================//
// 单选按钮值变化时
const radioChange = () => {
  ("day rachange");
  if (radioValue.value !== 2 && props.cron.week !== "?") {
    emit("update", "week", "?", "day");
  }

  switch (radioValue.value) {
    case 1:
      emit("update", "day", "*");
      break;
    case 2:
      emit("update", "day", "?");
      break;
    case 3:
      emit("update", "day", cycleTotal.value);
      break;
    case 4:
      emit("update", "day", averageTotal.value);
      break;
    case 5:
      emit("update", "day", workday.value + "W");
      break;
    case 6:
      emit("update", "day", "L");
      break;
    case 7:
      emit("update", "day", checkboxString.value);
      break;
  }
  ("day rachange end");
};
// 周期两个值变化时
const cycleChange = () => {
  if (radioValue.value === 3) {
    emit("update", "day", cycleTotal.value);
  }
};
// 平均两个值变化时
const averageChange = () => {
  if (radioValue.value === 4) {
    emit("update", "day", averageTotal.value);
  }
};
// 最近工作日值变化时
const workdayChange = () => {
  if (radioValue.value === 5) {
    emit("update", "day", workdayCheck.value + "W");
  }
};
// checkbox值变化时
const checkboxChange = () => {
  if (radioValue.value === 7) {
    emit("update", "day", checkboxString.value);
  }
};
//=======================** watch **===================================//
watch(radioValue, radioChange);
watch(cycleTotal, cycleChange);
watch(averageTotal, averageChange);
watch(workdayCheck, workdayChange);
watch(checkboxString, checkboxChange);
</script>
