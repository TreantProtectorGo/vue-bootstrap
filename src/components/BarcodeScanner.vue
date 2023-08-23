<template>
  <div id="qr-code-full-region">
    <div class="header" style="margin-bottom: 20px">{{ header }}</div>
    <div class="section" style="width: 500px; margin: auto">
      <qrcode-scanner
        v-bind:qrbox="250"
        v-bind:fps="10"
        style="width: 500px"
      ></qrcode-scanner>
    </div>
    <div class="footer" style="margin-bottom: 20px">{{ result }}</div>
  </div>
</template>

<script>
import { Html5QrcodeScanner } from "html5-qrcode";

export default {
  data() {
    return {
      header: "Html5-qrcode using vue.js",
      result: "",
    };
  },
  mounted() {},
  components: {
    "qrcode-scanner": {
      props: {
        qrbox: Number,
        fps: Number,
      },
      template: `<div id="qr-code-full-region"></div>`,
      mounted() {
        const $this = this;
        const config = { fps: this.fps ? this.fps : 10 };
        if (this.qrbox) {
          config.qrbox = this.qrbox;
        }

        function onScanSuccess(decodedText, decodedResult) {
          console.log(decodedResult);
          $this.$root.$emit("decodedCode", decodedText, decodedResult);
        }

        const html5QrcodeScanner = new Html5QrcodeScanner(
          "qr-code-full-region",
          config
        );
        html5QrcodeScanner.render(onScanSuccess);
      },
    },
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
}
.header {
  padding: 10px;
  background: black;
  color: white;
}
.section {
  padding: auto;
}
</style>
