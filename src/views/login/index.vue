<script lang="ts" setup>
import { login, register } from '@/api/home'
import appStore from '@/store'
import { OriginResponse } from '@/types/response'
import { Encrypt } from '@/utils/crypto'

const router = useRouter()
const { updateLoadingState, updateUserInfo } = appStore.useMainStore

const form = reactive({
  name: '',
  password: '',
  isRead: false,
})

const userRegister = () => {
  if (form.password === '' || form.name === '') return
  updateLoadingState(true)
  register({
    name: form.name,
    password: Encrypt(form.password.trim()),
  })
    .then(res => {
      Message.success(res.message)
      successLogin(res)
    })
    .catch(err => {
      console.log(err)
    })
    .finally(() => {
      updateLoadingState(false)
    })
}
const userLogin = () => {
  if (form.password === '' || form.name === '') return
  updateLoadingState(true)
  login({
    name: form.name,
    password: Encrypt(form.password.trim()),
  })
    .then(res => {
      Message.success(res.message)
      successLogin(res)
    })
    .catch(err => {
      console.log(err)
    })
    .finally(() => {
      updateLoadingState(false)
    })
}

const successLogin = (res: OriginResponse) => {
  updateUserInfo(res.result)
  router.push({
    name: 'dashboard',
  })
}

const homeWaveAreaKey = ref(0)
const homePageContext = ref('MR NOTICE')
const homeWaveCount = ref(2)
const handleWave = () => {
  homeWaveCount.value = 1
  homeWaveAreaKey.value += 1
}
</script>

<template>
  <div class="home">
    <div class="home-view" @click="handleWave">
      <div class="home-view-context" :key="homeWaveAreaKey">
        <div
          v-for="(item, index) in homePageContext.split('')"
          class="wave-node-animation"
          :key="item + index"
          :style="{
            animation: `wave 0.5s linear ${index * 60}ms ${homeWaveCount} alternate`,
            minWidth: '10px',
          }"
        >
          {{ item }}
        </div>
      </div>
    </div>
    <div class="home-content">
      <a-card hoverable :style="{ width: '400px' }">
        <template #cover>
          <div
            :style="{
              height: '204px',
              overflow: 'hidden',
            }"
          >
            <img
              :style="{ width: '100%', transform: 'translateY(-20px)' }"
              alt="dessert"
              src="https://p1-arco.byteimg.com/tos-cn-i-uwbnlip3yd/a20012a2d4d5b9db43dfc6a01fe508c0.png~tplv-uwbnlip3yd-webp.webp"
            />
          </div>
        </template>
        <div class="user-info-form">
          <a-form ref="formRef" :model="form" :style="{ width: '340px' }">
            <a-space direction="vertical" size="medium">
              <a-form-item field="name" label="用户名" validate-trigger="input" required>
                <a-input v-model="form.name" placeholder="请输入用户名..." allow-clear />
              </a-form-item>
              <a-form-item field="password" label="密码" validate-trigger="input" required>
                <a-input-password v-model="form.password" placeholder="请输入密码..." allow-clear />
              </a-form-item>
              <a-form-item>
                <a-space>
                  <a-button @click="userRegister">注册</a-button>
                  <a-button @click="userLogin">登录</a-button>
                </a-space>
              </a-form-item>
            </a-space>
          </a-form>
        </div>
      </a-card>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.home {
  display: flex;
  justify-content: space-around;
  width: 100vw;
  height: 100vh;

  .home-view {
    display: flex;
    align-items: center;
    overflow-x: hidden;
    width: 30%;
    min-width: 300px;
    height: 100%;
    background-color: bisque;

    .home-view-context {
      display: inline-block;
      width: 100%;
      font-size: 36px;
      text-align: center;
      color: brown;
      font-style: italic;

      .wave-node-animation {
        display: inline-block;
        user-select: none;
      }
    }
  }

  .home-content {
    display: flex;
    justify-content: center;
    align-items: center;
    overflow-x: hidden;
    width: 70%;
    min-width: 400px;
    height: 100%;
  }
}
</style>

<style lang="scss">
@import 'src/style/animation';

@include textWave;
</style>
