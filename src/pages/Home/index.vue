<template>
  <div class="main">
    <Particles id="tsparticles" class="main__particles" :options="particles" />

    <h3 class="welcome">欢迎来到我的个人网站<br />Welcome to my personal website</h3>

    <el-card class="box-card">
      <template #header>
        <div class="card-header">
          <span class="word-title">每日一言</span>
        </div>
      </template>
      <el-row>
        <el-col>《{{ word.from }}》</el-col>
        <el-col>{{ word.from_who }}</el-col>
        <el-col>{{ word.from_content }}</el-col>
      </el-row>
    </el-card>
    <el-row class="cert-flex" justify="center" align="middle">
      <span class="cert">网站备案号：</span>
      <el-link href="https://beian.miit.gov.cn" target="_blank">粤ICP备20019717号</el-link>
    </el-row>
  </div>
</template>

<script setup lang="ts">
  import {
    ref,
    reactive,
    defineComponent,
    computed,
    onMounted,
    toRefs,
    unref,
    watchEffect
  } from 'vue'
  import { useRouter } from 'vue-router'
  import { useMainStore } from "@/store/main"
  import axios from 'axios'
  import { useRequest } from 'vue-request'
  import { watch } from 'fs'
  import { particles } from '@/config/particles-config'

  type resultData = any

  // const mainStore = useMainStore()
  // console.log(mainStore.$state.count);

  const word = reactive({
    from: '',
    from_who: '',
    from_content: ''
  })
  const { data, loading } = useRequest(
    () => {
      return axios.get(`https://v1.hitokoto.cn`, {
        params: {
          c: 'k'
        }
      })
    },
    {
      pollingInterval: 20000
    }
  )

  watchEffect(() => {
    if (data.value) {
      const result: resultData = data.value
      // console.log(result);
      word.from = result?.data?.from
      word.from_who = result?.data?.from_who
      word.from_content = result?.data?.hitokoto
    }
  })
</script>
<style lang="less" scoped>
  .main {
    display: flex;
    flex-flow: column nowrap;
    align-items: start;
    justify-content: center;
    position: relative;
    height: 100%;
    width: 100%;
    overflow: hidden;
  }

  .welcome {
    margin: 6rem 0 0 2.5rem;
    color: #409eff;
  }

  .el-row {
    width: 100%;
  }

  .box-card {
    text-align: center;
    color: #666;
    width: 25rem;
    margin: 4.5rem auto 0;
    .word-title {
      font-size: 1.8rem;
      font-weight: bold;
    }

    .el-col {
      &:first-child {
        font-size: 1.4rem;
      }
      &:nth-child(2) {
        font-size: 0.9rem;
      }
      &:last-child {
        font-size: 1.1rem;
        margin-top: 1rem;
      }
    }
  }

  .cert-flex {
    position: fixed;
    bottom: 1rem;
    left: 50%;
    transform: translateX(-50%);
  }

  .cert {
    font-size: 0.95rem;
  }
</style>
