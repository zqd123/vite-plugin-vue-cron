<template>
  <el-form>
    <el-form-item>
      <el-radio v-model="radioValue" :label="1">
        秒，允许的通配符[, - * /]
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="2">
        周期从
        <el-input-number v-model="cycle01" :min="0" :max="58" /> -
        <el-input-number
          v-model="cycle02"
          :min="cycle01 ? cycle01 + 1 : 1"
          :max="59"
        />
        秒
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="3">
        从
        <el-input-number v-model="average01" :min="0" :max="58" /> 秒开始，每
        <el-input-number
          v-model="average02"
          :min="1"
          :max="59 - average01 || 0"
        />
        秒执行一次
      </el-radio>
    </el-form-item>

    <el-form-item>
      <el-radio v-model="radioValue" :label="4">
        指定
        <el-select
          clearable
          v-model="checkboxList"
          placeholder="可多选"
          multiple
          style="width: 100%"
        >
          <el-option v-for="item in 60" :key="item" :value="item - 1">{{
            item - 1
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
const props = defineProps(["check", "radioParent"]);
//=======================** data **===================================//
/**radio */
const radioValue = ref(1);
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
/**计算两个周期值 */
const cycleTotal = computed(() => {
  const _cycle01 = checkNum(cycle01.value, 0, 58);
  const _cycle02 = checkNum(cycle02.value, cycle01 ? _cycle01 + 1 : 1, 59);
  return _cycle01 + "-" + _cycle02;
});
// 计算平均用到的值
const averageTotal = computed(() => {
  const _average01 = checkNum(average01.value, 0, 58);
  const _average02 = checkNum(average02.value, 1, 59 - _average01 || 0);
  return _average01 + "/" + _average02;
});
// 计算勾选的checkbox值合集
const checkboxString = computed(() => {
  let str = checkboxList.value.join();
  return str == "" ? "*" : str;
});
//=======================** methods **===================================//
/**单选按钮值变化时 */
const radioChange = () => {
  switch (radioValue.value) {
    case 1:
      emit("update", "second", "*", "second");
      break;
    case 2:
      emit("update", "second", cycleTotal);
      break;
    case 3:
      emit("update", "second", averageTotal);
      break;
    case 4:
      emit("update", "second", checkboxString);
      break;
  }
};
/**周期两个值变化时 */
const cycleChange = () => {
  if (radioValue.value === 2) {
    emit("update", "second", cycleTotal);
  }
};
/**平均两个值变化时 */
const averageChange = () => {
  if (radioValue.value === 3) {
    emit("update", "second", averageTotal);
  }
};
/**checkbox值变化时 */
const checkboxChange = () => {
  if (radioValue.value == 4) {
    emit("update", "second", checkboxString);
  }
};
//=======================** methods **===================================//
watch(radioValue, radioChange);
watch(cycleTotal, cycleChange);
watch(averageTotal, averageChange);
watch(checkboxString, checkboxChange);
watch(props.radioParent, () => {
  radioValue.value = props.radioParent;
});
// export default {
//   data() {
//     return {
//       radioValue: 1,
//       cycle01: 1,
//       cycle02: 2,
//       average01: 0,
//       average02: 1,
//       checkboxList: [],
//       checkNum: this.$props.check,
//     };
//   },
//   name: "crontab-second",
//   props: ["check", "radioParent"],
//   methods: {
//     // 单选按钮值变化时
//     radioChange() {
//       switch (this.radioValue) {
//         case 1:
//           this.$emit("update", "second", "*", "second");
//           break;
//         case 2:
//           this.$emit("update", "second", this.cycleTotal);
//           break;
//         case 3:
//           this.$emit("update", "second", this.averageTotal);
//           break;
//         case 4:
//           this.$emit("update", "second", this.checkboxString);
//           break;
//       }
//     },
//     // 周期两个值变化时
//     cycleChange() {
//       if (this.radioValue == "2") {
//         this.$emit("update", "second", this.cycleTotal);
//       }
//     },
//     // 平均两个值变化时
//     averageChange() {
//       if (this.radioValue == "3") {
//         this.$emit("update", "second", this.averageTotal);
//       }
//     },
//     // checkbox值变化时
//     checkboxChange() {
//       if (this.radioValue == "4") {
//         this.$emit("update", "second", this.checkboxString);
//       }
//     },
//   },
//   watch: {
//     radioValue: "radioChange",
//     cycleTotal: "cycleChange",
//     averageTotal: "averageChange",
//     checkboxString: "checkboxChange",
//     radioParent() {
//       this.radioValue = this.radioParent;
//     },
//   },
//   computed: {
//     // 计算两个周期值
//     cycleTotal: function () {
//       const cycle01 = this.checkNum(this.cycle01, 0, 58);
//       const cycle02 = this.checkNum(
//         this.cycle02,
//         cycle01 ? cycle01 + 1 : 1,
//         59
//       );
//       return cycle01 + "-" + cycle02;
//     },
//     // 计算平均用到的值
//     averageTotal: function () {
//       const average01 = this.checkNum(this.average01, 0, 58);
//       const average02 = this.checkNum(this.average02, 1, 59 - average01 || 0);
//       return average01 + "/" + average02;
//     },
//     // 计算勾选的checkbox值合集
//     checkboxString: function () {
//       let str = this.checkboxList.join();
//       return str == "" ? "*" : str;
//     },
//   },
// };
</script>
