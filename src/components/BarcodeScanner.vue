<template>
  <div>
    <main class="wrapper">
      <section class="container" id="demo-content">
        <div class="card" style="width: 100%;">
          <video ref="video" style="border: 1px solid gray"></video>
          <div class="card-body">
            
            <div class="card-title">
              <div id="sourceSelectPanel" v-show="showSourceSelectPanel">
                <label for="sourceSelect">Change Camera:</label>
                <select id="sourceSelect" class="form-select" style="max-width:400px;" v-model="selectedDeviceId">
                  <option v-for="(device, index) in videoInputDevices" :key="index" :value="device.deviceId">
                    {{ device.label }}
                  </option>
                </select>
              </div>
            </div>
            <label for="result" style="display: none;">Result:</label>
            <pre style="display: none;">
              <span>Raw output:</span>
              <code id="result">{{ rawResult }}</code>
            </pre>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<script>
import productData from "../json/item.json";
import scanAudio from '@/assets/scan.mp3';

export default {
  data() {
    return {
      selectedDeviceId: '',
      videoInputDevices: [],
      showSourceSelectPanel: false,
      rawResult: '',
      productData: [],
    };
  },
  mounted() {
    this.initializeCodeReader();
    this.fetchData();
  },
  methods: {
    fetchData() {
      this.productData = productData;
    },
    playSound() {
      const audio = new Audio(scanAudio);
      audio.addEventListener('canplaythrough', () => {
        audio.play();
        audio.removeEventListener('canplaythrough', () => {});
      });
    },
    decode() {
      const codeReader = new ZXing.BrowserMultiFormatReader();
      codeReader.decodeFromVideoDevice(
        this.selectedDeviceId,
        this.$refs.video,
        (result, err) => {
          if (result) {
            console.log(result);
            let re = new RegExp(String.fromCharCode(29), 'g');
            this.rawResult = result.text.replace(re, '<GS>');

            this.playSound(); 
            this.$emit('scanned', this.rawResult); // Emit the 'scanned' event with the scanned data
          }
          if (err && !(err instanceof ZXing.NotFoundException)) {
            console.error(err);
            this.rawResult = err;
          }
        }
      );
    },
    initializeCodeReader() {
      const codeReader = new ZXing.BrowserMultiFormatReader();
      console.log('ZXing code reader initialized');

      codeReader.getVideoInputDevices().then((videoInputDevices) => {
        this.videoInputDevices = videoInputDevices;
        this.selectedDeviceId = videoInputDevices[0].deviceId;
        this.showSourceSelectPanel = videoInputDevices.length >= 1;

        this.decode();

        console.log(`Started continuous decode from camera with id ${this.selectedDeviceId}`);
      }).catch((err) => {
        console.error(err);
      });
    },
  },
};
</script>
his.$emit('scanned', this.rawResult);