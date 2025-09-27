<script setup>
import { ref } from 'vue'
import { Button, IftaLabel, InputText, Password } from 'primevue'
import router from '@/router/router'

const isInitPanelEnabled = ref(true)
const isMainPanelEnabled = ref(false)

const usernameValue = ref()
const passwordValue = ref()

async function login() {
  router.push('/main')
}

async function register() {
  router.push('/main')
}

function showMainPanel() {
  isInitPanelEnabled.value = false
  setTimeout(() => {
    isMainPanelEnabled.value = true
  }, 600)
}
</script>

<template>
  <div id="bgPanel">
    <Transition name="fade-scale">
      <div id="initPanel" v-if="isInitPanelEnabled">
        <Button label="Authorize" size="large" @click="showMainPanel" severity="info" />
      </div>
    </Transition>
    <Transition name="fade-blur">
      <div id="mainPanel" v-if="isMainPanelEnabled">
        <div id="mainPanelContents">
          <h3 id="authHeader">Sign in or register</h3>
          <IftaLabel>
            <InputText id="usernameInput" v-model="usernameValue" variant="filled"></InputText>
            <label for="usernameInput">Username</label>
          </IftaLabel>
          <IftaLabel>
            <Password
              id="passwordInput"
              v-model="passwordValue"
              variant="filled"
              :feedback="false"
            ></Password>
            <label for="passwordInput">Password</label>
          </IftaLabel>
          <div id="buttonPanel">
            <Button id="loginButton" label="Sign in" @click="login" severity="secondary"></Button>
            <Button
              id="registerButton"
              label="Register"
              @click="register"
              severity="secondary"
            ></Button>
          </div>
        </div>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
/* Panel styles and arrangement */

#bgPanel {
  background-image: url('src/assets/BG1.jpg');
  background-size: cover;
  background-repeat: no-repeat;

  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
}

#initPanel {
  position: fixed;
  top: 20%;
  left: 50%;
  transform: translate(-50%, -50%);

  display: flex;
  justify-content: center;
  align-items: center;
}

#mainPanel {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;

  display: flex;
  justify-content: center;

  background: rgba(70, 70, 70, 0.25);
  backdrop-filter: blur(10px);
}

#mainPanelContents {
  position: fixed;
  top: 5%;
}

#buttonPanel {
  display: flex;
  justify-content: space-evenly;

  width: 100%;
}

#mainPanelContents > * {
  margin-top: 10px;
}

#authHeader {
  text-align: center;
  color: rgb(185, 185, 185);
}

/* Specific element styles */

:deep(.p-inputtext, .p-password) {
  background-color: rgba(0, 0, 0, 0.377) !important;
}

/* Animations */

.fade-scale-enter-active,
.fade-scale-leave-active {
  transition: all 0.6s ease;
}

.fade-scale-enter-from,
.fade-scale-leave-to {
  opacity: 0;
  transform: translate(-50%, -50%) scale(0.8);
}

.fade-scale-enter-to,
.fade-scale-leave-from {
  opacity: 1;
  transform: translate(-50%, -50%) scale(1);
}

.fade-blur-enter-active,
.fade-blur-leave-active {
  transition: all 0.8s ease;
}

.fade-blur-enter-from,
.fade-blur-leave-to {
  opacity: 0;
  backdrop-filter: blur(0px);
  background: rgba(70, 70, 70, 0);
}

.fade-blur-enter-to,
.fade-blur-leave-from {
  opacity: 1;
  backdrop-filter: blur(10px);
  background: rgba(70, 70, 70, 0.25);
}
</style>
