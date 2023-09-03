<template>
  <div class="container">
    
    <div id="reader"></div>
  </div>
</template>

<script setup lang="ts">
//interface.ts vite-env.d.ts

import { onMounted, ref, onUnmounted } from 'vue'
// import { useRouter } from 'vue-router'
import { Html5Qrcode } from 'html5-qrcode'
import { Html5QrcodeResult, CameraDevice } from './interface'

const cameraId = ref('')
const devicesInfo = ref<any>('')
const html5QrCode = ref<any>(null)
// const router = useRouter()

onMounted(() => {
  getCameras()
})

onUnmounted(() => {
  stop()
})

const getCameras = () => {
  Html5Qrcode.getCameras()
    .then((devices: CameraDevice[]) => {
      console.log('camera information', devices)
      if (devices && devices.length) {
        // 1為前置
        if (devices.length > 1) {
          cameraId.value = devices[1].id
        } 
        else {
          cameraId.value = devices[0].id
        }
        devicesInfo.value = devices
        // start scan
        start()
      }
    })
    .catch((err) => {
      // handle err
      console.log('Failed to get device information', err) 
    })
}
const start = () => {
  html5QrCode.value = new Html5Qrcode('reader')
  console.log('html5QrCode', html5QrCode)

  html5QrCode.value.start(
    cameraId.value, // retreived in the previous step.
    {
      fps: 10, // 設置每秒多少幀
      qrbox: { width: 250, height: 150 } // 設置取景範圍
      // scannable, rest shaded.
    },
    (decodedText: string, decodedResult: Html5QrcodeResult) => {
      // do something when code is read. For example:
      // if (qrCodeMessage) {
      //   getCode(qrCodeMessage);
      //   stop();
      // }
      console.log('scan results', decodedText, decodedResult)
      // if (decodedText) {
      //   router.push('order');
      // }

      if (decodedText) {
        this.$emit('scanned', decodedText);
      }
    },
    (errorMessage: any) => {
      // parse error, ideally ignore it. For example:
      // console.log(`QR Code no longer in front of camera.`);
      console.log('No scan results', errorMessage)
    }
  )
    .catch((err: any) => {
      // Start failed, handle it. For example,
      console.log(`Unable to start scanning, error: ${err}`)
    })
}
const stop = () => {
  html5QrCode.value
    .stop()
    .then((ignore: any) => {
      // QR Code scanning is stopped.
      console.log('QR Code scanning stopped.', ignore)
    })
    .catch((err: any) => {
      // Stop failed, handle it.
      console.log('Unable to stop scanning.', err)
    })
}
</script>

<style scoped>
.container {
  /* position: relative; */
  height: 100%;
  width: 100%; 
  background: rgba(#000000, 0.48);
}
#reader {
  /* top: 50%;
  left: 0;
  transform: translateY(-50%); */
}
</style>