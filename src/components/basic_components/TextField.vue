<script setup>
import { IftaLabel, InputText } from 'primevue'
import { ref } from 'vue'

defineProps(['description'])

const inputTextValue = ref('')
</script>

<template>
  <IftaLabel>
    <InputText id="textField" v-model="inputTextValue" variant="filled" />
    <label for="textField">{{ description }}</label>
  </IftaLabel>
</template>
