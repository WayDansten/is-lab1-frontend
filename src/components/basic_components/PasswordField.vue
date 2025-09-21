<script setup>
import { IftaLabel, Password } from 'primevue'
import { ref } from 'vue'

defineProps(['description'])

const inputPasswordValue = ref('')
</script>

<template>
  <IftaLabel>
    <Password
      inputId="passwordField"
      v-model="inputPasswordValue"
      variant="filled"
      :feedback="false"
    />
    <label for="passwordField">{{ description }}</label>
  </IftaLabel>
</template>
