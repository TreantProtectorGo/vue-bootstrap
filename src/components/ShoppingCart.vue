<style>
  .btn-primary {
    background-color: #094FA3;
  }
  .btn-floating {
    position: fixed;
    bottom: 20px;
    right: 20px;
    z-index: 9999;
    margin-bottom: 50px;
  }
  .xs {
   font-size: 12px;
  }
  .material-icons {
    vertical-align: middle;
  }
  .title {
    font-weight: bold;
  }
  body {
    font-family: 'General Sans', sans-serif;
  }

  #snackbar {
    visibility: hidden;
    min-width: 250px;
    margin-left: -125px;
    background-color: #094FA3;
    color: #fff;
    text-align: center;
    border-radius: 2vh;
    padding: 16px;
    position: fixed;
    z-index: 1;
    left: 50%;
    bottom: 30px;
    font-size: 17px;
    margin-bottom: 12vh;
  }

  #snackbar.show {
    visibility: visible;
    -webkit-animation: fadein 0.5s, fadeout 0.5s 2.5s;
    animation: fadein 0.5s, fadeout 0.5s 2.5s;
  }

  @-webkit-keyframes fadein {
    from {bottom: 0; opacity: 0;} 
    to {bottom: 30px; opacity: 1;}
  }

  @keyframes fadein {
    from {bottom: 0; opacity: 0;}
    to {bottom: 30px; opacity: 1;}
  }

  @-webkit-keyframes fadeout {
    from {bottom: 30px; opacity: 1;} 
    to {bottom: 0; opacity: 0;}
  }

  @keyframes fadeout {
    from {bottom: 30px; opacity: 1;}
    to {bottom: 0; opacity: 0;}
  }
</style>

<template>
  <header>
    <!-- Just an image -->
    <nav class="navbar navbar-expand-md navbar-light bg-light" style="max-height: 45px;">
      <div class="navbar-collapse collapse w-100 order-1 order-md-0 dual-collapse2">
          <ul class="navbar-nav text-start">
              <li class="nav-item active">
                <img src="/src/assets/logo.png" width="100" height="100%" alt="">
              </li>
          </ul>
      </div>
      <div class="mx-auto order-0">
          <a class="navbar-brand mx-auto title fs-5">Shopping Cart</a>
      </div>
      <div class="navbar-collapse collapse w-100 order-3 dual-collapse2">
          <ul class="navbar-nav ms-auto">
              <li class="nav-item" >
                <i class="material-icons nav-link py-0 pe-4" style="font-size: 40px;">account_circle</i>
              </li>
          </ul>
      </div>
    </nav>
  </header>
  
  <button @click="addToast()">Show Snackbar</button>
  <div id="snackbar">sd</div>
  

  <button class="btn btn-primary btn-floating p-2 rounded-circle"  @click="clickQr(); openScanner();" data-bs-toggle="modal" data-bs-target="#scanBarcodeDialog">
    <i class="material-icons">add</i>
  </button>

  <div class="modal fade" id="scanBarcodeDialog" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLongTitle">Scan Code</h5>
          <button @click="closeQr" type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <screenqrcode @scanned="handleScannedData" @ok="getResult" @err="geterror" v-if="open"></screenqrcode>
        </div>
      </div>
    </div>
  </div>

  <div class="input-group mb-3 p-3">
    <input type="text"  class="form-control" placeholder="Enter Barcode" aria-label="Enter Barcode" aria-describedby="button-addon2" id="barcode" >
    <button class="btn btn-primary" type="button" id="button-addon2" v-on:click="addItem">Add</button>
  </div>

  <div v-if="cartItems.length === 0" >
      <br /> <br /> <br />
      <div class="container text-center">
        <div class="row align-items-start">
          <i class="material-icons" style="color: #094FA3; font-size: 80px;">remove_shopping_cart</i>
          <p class="fs-3 fw-bold" style="color: #094FA3;" >Hi! What are you looking for today?</p>
        </div>
      </div>
  </div>

  <section class="h-100" style="" v-if="cartItems.length != 0">
    <div class="h-100">
      <div class="row d-flex justify-content-center align-items-center h-100">
        <div class="col-11">

          <!-- <div class="d-flex justify-content-between align-items-center mb-4">
            <h3 class="fw-normal mb-0 text-black">Shopping Cart</h3>
            <div>
              <p class="mb-0"><span class="text-muted">Sort by:</span> <a href="#!" class="text-body">price <i
                    class="fas fa-angle-down mt-1"></i></a></p>
            </div>
          </div> -->

          <div class="card rounded-3 mb-4 ">
            <div class="card-body p-1" v-for="(item, index) in cartItems" :key="index">
              <div class="row d-flex justify-content-between align-items-center">
                <div class="col-6 col-md-3">
                  <img
                    :src="item.img"
                    class="img-fluid rounded-3" alt="Product Image">
                </div>
                <div class="col-6 col-md-5">
                  <p class="lead fw-bold mb-2">{{ item.name }}</p>
                  <p><span class="text-muted">{{ item.barcode }}</span></p>
                  <div class="input-group input-group-sm">
                    <div class="input-group-prepend" >
                      <span class="input-group-text" id="inputGroup-sizing" > 
                        <i class="material-icons xs" @click="handleAddCount(index)" >add</i>
                      </span>
                    </div>
                    <input v-model="item.count"  style="max-width: 50px; max-height: 25px;" type="text" class="form-control text-center" disabled readonly>
                    <div class="input-group-append">
                      <span class="input-group-text" id="inputGroup-sizing-default"> 
                        <i class="material-icons xs" @click="handleReduceCount(index)" style="width: 1vh;">remove</i>
                      </span>
                    </div>
                  </div>
                </div>
                <div class="col-9 text-end col-md-2 text-right">
                  <h5 class="mb-0">$ {{ itemTotalPrice(index) }}</h5>
                </div>
                <div class="col-3 col-md-1 d-flex">
                  <a href="#!" class="text-danger">
                    <i class="material-icons" data-bs-toggle="modal" data-bs-target="#deleteDialog">delete</i>
                  </a>
                </div>
              </div>
            </div>
          </div>

          <div class="modal fade" id="deleteDialog" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <h1 class="modal-title fs-5" id="staticBackdropLabel">Delete Product</h1>
                  <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                  Are you sure you want to delete this Product?
                </div>
                <div class="modal-footer">
                  <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                  <button type="button" class="btn btn-primary" data-bs-dismiss="modal" @click="removeItem(index)" >Delete</button>
                </div>
              </div>
            </div>
          </div>
          <div class="">
            <div class="input-group mb-3">
              <input type="text" class="form-control" placeholder="Discound code" aria-label="Discound code" aria-describedby="basic-addon2">
              <div class="input-group-append">
                <button class="btn btn-primary text-white" type="button">Apply</button>
              </div>
            </div>

            <div class="mb-5 pb-5">
              <div class="card">
                <div class="card-body">
                  <button type="button" class="btn btn-primary btn-lg w-100">Proceed to Pay  $ {{ cartTotalPrice() }}</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <footer>
      <nav
        class="nav border-top nav-pills nav-justified fixed-bottom flex-nowrap" 
        
      >
      <div class="btn-group" role="group" style="width:100%;" >
        <a href="https://treantprotectorgo.github.io/html-home/" class="btn btn-light" >
          <i style="display: flex; flex-direction: column; font-size: 28px;" class="material-icons">home</i>
          Home</a>
          
        <a href="https://nevercod.github.io/chat/" class="btn btn-light">
          <i class="material-icons" style="display: flex; flex-direction: column; font-size: 28px;">chat</i>
          Chat
        </a>
        
        <a href="#" class="btn btn-light" style=" color: #094FA3;">
          <i class="material-icons" style="display: flex; flex-direction: column; font-size: 28px;">shopping_cart</i>
          Cart
        </a>
      </div>

    </nav>
  </footer>

</template>


<script>
import productData from "../json/item.json";
import BarcodeScanner from "./BarcodeScanner.vue";

import screenqrcode from "./screenqrcode.vue";
import qrcode from "./qrcode.vue";

var browser = {  
  versions: (function () {
    var u = navigator.userAgent;
    return {
      trident: u.indexOf('Trident') > -1, //IE
      presto: u.indexOf('Presto') > -1, //opera
      webKit: u.indexOf('AppleWebKit') > -1, //apple, google
      gecko: u.indexOf('Gecko') > -1 && u.indexOf('KHTML') == -1, //firefox
    };
  })(),
};

export default {
  data() {
    return {
      input: "",
      barcode: "",
      cartItems: [],
      confirm: [],
      productData: [],
      showScanner: false,
      scanSuccess: false,
      open: false,
    };
  },
  components: { 
    BarcodeScanner,
    screenqrcode,
    qrcode
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    addToast(data) {
      document.getElementById("snackbar").innerHTML = data;
      var x = document.getElementById("snackbar");
      x.className = "show";
      setTimeout(function(){ x.className = x.className.replace("show", ""); }, 3000);
    },
    clickQr() {
      // check browser
      if (
        browser.versions.webKit ||
        browser.versions.gecko
      ) {
        this.open = true;
      } else {
        alert('browser not support');
      }
    },
    closeQr() {
      // check browser
      if (
        browser.versions.webKit ||
        browser.versions.gecko
      ) {
        this.open = false;
      } else {
        alert('browser not support');
      }
    },
    getResult(result) {
      const r = result.split('?')[1].split('=');
      if (r[1] == 'nX8ERbN9t4EZdPbNxMirW3uYS71111') {
        this.open = false;
      } else {
        this.open = false;
        alert('try again');
      }
    },
    geterror: function(err) {
      alert(err);
    },
    handleScannedData(data) {
      console.log('Scanned data:', data);
      this.barcode = data;
      this.scanSuccess = true;
      this.addItem();
      // Process the scanned data in the parent component
    },
    fetchData() {
      this.productData = productData;
    },
    openScanner() {
      this.showScanner = true;
    },
    addItem() {
      if (!this.scanSuccess) {
        this.barcode = document.getElementById("barcode").value;
      }
      if (this.barcode) {
        const product = this.productData.find((product) => product.gtin === this.barcode); 
        if (product) {
          const existingItemIndex = this.cartItems.findIndex((item) => item.barcode === this.barcode);
          this.input = "";
          if (existingItemIndex !== -1) {
            this.cartItems[existingItemIndex].count++;
          } else {
            this.cartItems.push({ barcode: this.barcode, count: 1, name: product.name, img: product.img, price: product.price });
            this.confirm.push(false);
          }
        }
        this.barcode = "";
        this.addToast("Success");
      } else {
        this.addToast("Unsuccess");
      }
      // this.scanSuccess = false;
    },
    removeItem(index) {
      this.cartItems.splice(index, 1);
      this.confirm.splice(index, 1);
    },
    cartTotalPrice() {
      let totalPrice = 0;
      for (let item of this.cartItems) {
        totalPrice += item.price * item.count;
      }
      return totalPrice.toFixed(2);
    },
    itemTotalPrice(index) {
      let itemTotalPrice = 0;
      itemTotalPrice = this.cartItems[index].price * this.cartItems[index].count;
      return itemTotalPrice.toFixed(2);
    },
    handleAddCount(index) {
      this.cartItems[index].count += 1;
    },
    handleReduceCount(index) {
      if (this.cartItems[index].count === 1) {
        return;
      }
      else if (this.cartItems[index].count > 1) {
        this.cartItems[index].count--;
      } else {
        this.cartItems.splice(index, 1);
      }
    },
  },
};
</script>
