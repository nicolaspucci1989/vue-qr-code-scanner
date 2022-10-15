<script>
import {QrcodeStream} from 'qrcode-reader-vue3'
import {GDialog} from "gitart-vue-dialog";

export default {
  components: {QrcodeStream, GDialog},
  data() {
    return {
      isValid: undefined,
      camera: 'auto',
      result: null,
      order: null,
      dialogState: false
    }
  },
  computed: {
    validationPending() {
      return this.isValid === undefined
          && this.camera === 'off'
    },
    validationSuccess() {
      return this.isValid === true
    },
    validationFailure() {
      return this.isValid === false
    }
  },
  methods: {
    onInit(promise) {
      promise
          .catch(console.error)
          .then(this.resetValidationState)
    },
    resetValidationState() {
      this.isValid = undefined
    },
    async onDecode(payload) {
      this.result = payload
      this.turnCameraOff()
      this.dialogState = true
      this.isValid = true
      this.turnCameraOn()
    },
    turnCameraOn() {
      this.camera = 'auto'
    },
    turnCameraOff() {
      this.camera = 'off'
    },
  }
}
</script>

<template>
  <div v-if="order === null">
    <p class="decode-result">Last result: <b>{{ result }}</b></p>

    <QrcodeStream :camera="camera" @decode="onDecode" @init="onInit">
      <div v-if="validationSuccess" class="validation-success">
        <pre>
          {{ this.order }}
        </pre>
      </div>

      <div v-if="validationFailure" class="validation-failure">
        This is NOT a URL!
      </div>

      <div v-if="validationPending" class="validation-pending">
        Long validation in progress...
      </div>
    </QrcodeStream>
  </div>

  <GDialog v-model="dialogState">
    <div class="dialog">
      <h2>
        Dialog Content
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

.validation-success, .validation-failure, .validation-pending {
  position: absolute;
  width: 100%;
  height: 100%;

  background-color: rgba(255, 255, 255, .8);
  text-align: center;
  font-weight: bold;
  font-size: 1.4rem;
  padding: 10px;

  display: flex;
  flex-flow: column nowrap;
  justify-content: center

}
.validation-success {
  color: green;
}
.validation-failure {
  color: red;
}
</style>
