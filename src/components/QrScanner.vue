<script setup>
import {reactive} from "vue";
import {QrcodeStream} from 'qrcode-reader-vue3'
import {GDialog} from "gitart-vue-dialog";

const state = reactive({
  camera: 'auto',
  result: null,
  order: null,
  dialogState: false
})

function onInit(promise) {
  promise
      .catch(console.error)
      .then(resetValidationState)
}

function resetValidationState() {
  state.isValid = undefined
}

function onDecode(payload) {
  state.result = payload
  turnCameraOff()
  state.dialogState = true
}

function turnCameraOn() {
  state.camera = 'auto'
}

function turnCameraOff() {
  state.camera = 'off'
}
</script>

<template>
  <div v-if="state.order === null">
    <p class="decode-result">Last result: <b>{{ state.result }}</b></p>

    <QrcodeStream :camera="state.camera" @decode="onDecode" @init="onInit"/>
  </div>

  <GDialog v-model="state.dialogState">
    <div class="dialog">
      <h2>
        {{state.result}}
      </h2>

      <p>Lorem ipsum dolor sit amet.</p>
    </div>
  </GDialog>
</template>

<style scoped>
@import 'https://cdn.jsdelivr.net/npm/gitart-vue-dialog@1.2.1/dist/style.css';

.dialog {
  padding: 30px 20px;
}

h2 {
  margin: 0 0 20px;
}
</style>
