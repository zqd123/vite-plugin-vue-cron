<script setup lang="ts">
import { ref } from "vue";
import HelloWorld from "./components/HelloWorld.vue";
import Crontab from "./Crontab/index.vue";

const cron = ref("");
// 是否显示Cron表达式弹出层
const openCron = ref(false);
// 传入的表达式
const expression = ref("");
// 表单参数
const form = ref({
  cronExpression: "",
});
/** cron表达式按钮操作 */
const handleShowCron = () => {
  expression.value = form.value.cronExpression;
  openCron.value = true;
};
/** 确定后回传值 */
const crontabFill = (value: string) => {
  form.value.cronExpression = value;
};
</script>

<template>
  <!-- <div class="flex p-2">
    <label class="w-40">cron表达式：</label>
    <el-input v-model="cron"></el-input>
  </div> -->
  <!-- <Crontab></Crontab> -->

  <el-input v-model="form.cronExpression" placeholder="请输入CRON 表达式">
    <template #append>
      <el-button type="primary" @click="handleShowCron">
        生成表达式
        <i class="el-icon-time el-icon--right"></i>
      </el-button>
    </template>
  </el-input>
  <el-dialog
    title="Cron表达式生成器"
    v-model="openCron"
    :align-center="true"
    append-to-body
    class="scrollbar"
    destroy-on-close
  >
    <Crontab
      @hide="openCron = false"
      @fill="crontabFill"
      :expression="expression"
    ></Crontab>
  </el-dialog>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
