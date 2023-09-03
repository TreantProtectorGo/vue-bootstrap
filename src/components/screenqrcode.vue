<template>
    <div class="qrcode">
      <div id="reader"></div>
    </div>
  </template>
  
  <script>
    import { Html5Qrcode } from 'html5-qrcode';
    export default {
      created() {
        this.getCameras();
      },
      onUnmounted () {
        this.stop();
      },
      methods: {
        getCameras() {
          Html5Qrcode.getCameras()
            .then((devices) => {
              if (devices && devices.length) {
                this.html5QrCode = new Html5Qrcode('reader');
                this.start();
              }
            })
            .catch((err) => {
              // handle err
              this.html5QrCode = new Html5Qrcode('reader');
              this.error = 'ERROR: you need to grant camera access permission';
              this.$emit('err', this.error, err);
            });
        },
        start() {
          //environment後置 user前置
          this.html5QrCode
            .start(
              { facingMode: 'environment' },
              {
                fps: 2,    
                qrbox: { width: 250, height: 150 }, 
              },
              (decodedText) => {
                this.$emit('scanned', decodedText);
                this.$emit('ok', decodedText);
              },
            )
            .catch((err) => {
              this.$emit('err', err);
            });
        },
        stop() {
          this.html5QrCode
            .stop()
            .then((ignore) => {
              // QR Code scanning is stopped.
              this.$emit('err', ignore);
              console.log('QR Code scanning stopped.');
            })
            .catch((err) => {
              this.$emit('err', err);
              // Stop failed, handle it.
              console.log('Unable to stop scanning.');
            });
        },
      },
    };
  </script>
  
  <style lang="scss" scoped>
    .qrcode {
      position: relative;
      height: 100%;
      width: 100%;
      background: rgba($color: #000000, $alpha: 0.48);
    }
    #reader {
    //   top: 50%;
    //   left: 0;
    //   transform: translateY(-50%);
    }
  </style>
  
  