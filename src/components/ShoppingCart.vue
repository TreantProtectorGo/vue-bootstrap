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
</style>

<template>
  <button class="btn btn-primary btn-floating" @click="openScanner">
    <i class="material-icons">add</i>
    <BarcodeScanner v-if="showScanner" />
  </button>

  <div class="input-group mb-3">
    <input type="text"  class="form-control" placeholder="Enter Barcode" aria-label="Enter Barcode" aria-describedby="button-addon2" id="barcode" v-model="input">
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

  <div class="card mb-3" v-for="(item, index) in cartItems" :key="index">
    <div class="row g-0">
      <div class="col-md-3">
        <img :src="item.img" alt="Product Image" class="img-fluid rounded-start">
      </div>
      <div class="col-md-1">
      </div>
      <div class="col-md-5">
        <div class="card-body">
          <h5 class="card-title">{{ item.name }}</h5>
          <p class="card-text">{{ item.barcode }}</p>
          <p class="card-text fs-4 fw-bold" style="color: red;" >$ {{ item.price }}</p>
          <!-- <p class="card-text"><small class="text-body-secondary">Last updated 3 mins ago</small></p> -->
        </div>
      </div>
      <div class="col-md-3">
        <div class="card-body" style="margin-top: 30px;">
          <button type="button" class="btn btn-light card-title" data-bs-toggle="modal" data-bs-target="#deleteDialog" style="width: 100%;">Delete</button>
          <div class="input-group mb-3">
              <div class="input-group-prepend" >
                <button type="button" class="btn btn-light" @click="handleAddCount(index)" style="width: 38px">+</button>
              </div>
              <input type="text" class="form-control text-center" v-model="item.count" >
              <div class="input-group-append">
                <button type="button" class="btn btn-light" @click="handleReduceCount(index)" style="width: 38px">-</button>
              </div>
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
  </div>

</template>


<script>
import productData from "../json/item.json";
import BarcodeScanner from "./BarcodeScanner.vue";

export default {
  data() {
    return {
      input: "",
      barcode: "",
      cartItems: [],
      confirm: [],
      productData: [],
      showScanner: false
    };
  },
  components: { 
    BarcodeScanner
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      this.productData = productData;
    },
    openScanner() {
      this.showScanner = true;
    },
    // addItem() {
    //   if (this.barcode) {
    //     this.cartItems.push({ barcode: this.barcode, count: 1 });
    //     this.confirm.push(false);
    //     this.barcode = "";
    //   }
    // },
    addItem() {
      this.barcode = document.getElementById("barcode").value;
      console.log(this.barcode);
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
            this.barcode = "";
          }
          
        } else {
        
        }
      }
    },
    removeItem(index) {
      this.cartItems.splice(index, 1);
      this.confirm.splice(index, 1);
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
