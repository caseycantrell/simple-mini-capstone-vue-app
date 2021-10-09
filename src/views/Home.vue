<template>
  <div class="home">
    <h1>Create Product</h1>
    <div v-for="error in errors" v-bind:key="error">
      <li>{{ error }}</li>
    </div>
    <div>
      Name:
      <input type="text" v-model="newProductParams.name" />
    </div>
    <div>
      Image Url:
      <input type="text" v-model="newProductParams.image_url" />
    </div>
    <div>
      Description:
      <input type="text" v-model="newProductParams.description" />
    </div>
    <div>
      Price:
      <input type="text" v-model="newProductParams.price" />
    </div>

    <br />
    <button v-on:click="createProduct()">Create</button>
    <h1>Check Out These Killer Products</h1>
    <div v-for="product in products" v-bind:key="product.id">
      <h2>Name: {{ product.name }}</h2>
      <img v-bind:src="product.image_url" v-bind:alt="product.title" />
      <br />
      <br />
      <button v-on:click="showProduct(product)">Mo' Details</button>
    </div>
    <dialog id="product-details">
      <form method="dialog">
        <h1>Product Details</h1>
        <p>
          Name:
          <input type="text" v-model="currentProduct.name" />
        </p>
        <img :src="currentProduct.image_url" alt="" />
        <p>
          Image URL:
          <input type="text" v-model="currentProduct.image_url" />
        </p>
        <p>
          Description:
          <input type="text" v-model="currentProduct.description" />
        </p>
        <p>
          Price:
          <input type="text" v-model="currentProduct.price" />
        </p>
        <button v-on:click="updateProduct(currentProduct)">Update</button>
        &nbsp;
        <button v-on:click="destroyProduct(currentProduct)">Destroy</button>
        &nbsp;
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style scoped>
img {
  width: 300px;
}
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      products: [],
      newProductParams: {},
      currentProduct: {},
      errors: [],
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    indexProducts: function () {
      axios.get("http://localhost:3000/products").then((response) => {
        console.log(response.data);
        this.products = response.data;
      });
    },
    createProduct: function () {
      axios
        .post("http://localhost:3000/products", this.newProductParams)
        .then((response) => {
          console.log(response.data);
          this.products.push(response.data);
        })
        .catch((error) => {
          this.errors = error.response.data.errors;
        });
    },
    showProduct: function (product) {
      console.log(product);
      this.currentProduct = product;
      document.querySelector("#product-details").showModal();
    },
    updateProduct: function (product) {
      axios
        .patch(`http://localhost:3000/products/${product.id}`, product)
        .then((response) => {
          console.log(response.data);
        })
        .catch((error) => {
          console.log(error.response.data.errors);
        });
    },
    destroyProduct: function (product) {
      axios.delete(`http://localhost:3000/products/${product.id}`).then((response) => {
        console.log("Successfully destroyed", response.data);
        var index = this.products.indexOf(product);
        this.products.splice(index, 1);
      });
    },
  },
};
</script>
