<template>
    <QrScan ref="qrcode" @ok="getResult" @err="geterror" v-if="open" />
     <div class="home-item" @click="clickQr"> 
        <span class="btn"> </span>
	 </div>
</template>
<script setup lang="ts">
  import QrScan from './screenqrcode.vue';
  import { ref } from 'vue';
  const open = ref(false);
  var browser = {  
    versions: (function () {
      var u = navigator.userAgent;
      return {
        trident: u.indexOf('Trident') > -1, //IE
        presto: u.indexOf('Presto') > -1, //opera
        webKit: u.indexOf('AppleWebKit') > -1, //apple, google
        gecko: u.indexOf('Gecko') > -1 && u.indexOf('KHTML') == -1, //firefox
        weixin: u.indexOf('MicroMessenger') > -1, 
      };
    })(),
  };
  const clickQr = () => {
    // check browser
    if (browser.versions.webKit || browser.versions.weixin || browser.versions.gecko) {
      open.value = true;
    } else {
      alert('browser not support');
    }
  };
  const getResult = (result) => {
    const r = result.split('?')[1].split('=');   
    if (r[1] == 'nX8ERbN9t4EZdPbNxMirW3uYS71111') {  
    	// 根據內容生成的二維碼，我們規定只有內容識別為這個，才可以通過，否則提示非法通行碼
        open.value = false;
    } else {
      open.value = false;
      alert('try again');
    }
  };
  const geterror = (err) => {
    alert(err);  
  };
</script>