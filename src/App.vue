<script setup lang="ts">
import { ref } from 'vue';
import HelloWorld from './components/HelloWorld.vue'
import Crontab from './Crontab/index.vue'

const cron = ref('')
</script>

<template>
  <div class="flex p-2">
    <label class="w-40">cron表达式：</label>
    <el-input v-model="cron"></el-input>
  </div> 
  <Crontab></Crontab>
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
