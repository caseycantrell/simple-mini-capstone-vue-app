<template>
  <div class="home">
    <h1 class="animate__animated animate__rubberBand animate__infinite animate__slower">Create Product</h1>
    <img v-if="status" :src="`https://http.cat/${status}`" alt="" />
    <div v-for="error in errors" v-bind:key="error">
      {{ error }}
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
    <h1 class="animate__animated animate__jello animate__infinite animate__slower">Check Out These Killer Products</h1>

    <div>
      Search:
      <input type="text" v-model="nameFilter" list="names" />
    </div>
    <datalist id="names">
      <option v-for="product in products" v-bind:key="product.id">
        {{ product.name }}
      </option>
    </datalist>
    <br />
    <button v-on:click="sortAttribute = 'name'">Sort by Name</button>
    &nbsp;
    <button v-on:click="sortAttribute = 'price'">Sort by Price</button>
    <div
      is="transition-group"
      appear
      enter-active-class="animate__animated animate__fadeIn"
      leave-active-class="animate__animated animate__fadeOut"
    >
      <div
        v-for="product in orderBy(filterBy(products, nameFilter, 'name'), sortAttribute, sortOrder)"
        v-bind:key="product.id"
      >
        <h2>Name: {{ product.name }}</h2>
        <img v-bind:src="product.image_url" v-bind:alt="product.title" />
        <br />
        <br />
        <button v-on:click="showProduct(product)">Mo' Details</button>
      </div>
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
        <br />
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
import Vue2Filters from "vue2-filters";

export default {
  mixins: [Vue2Filters.mixin],
  data: function () {
    return {
      products: [],
      newProductParams: {},
      currentProduct: {},
      errors: [],
      status: null,
      sortAttribute: "name",
      nameFilter: "",
      sortOrder: 1,
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
          window.alert("New product successfully created! Way to go champ!");
        })
        .catch((error) => {
          this.status = error.response.status;
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
      if (confirm("Are you sure you want to delete that?")) {
        axios.delete(`http://localhost:3000/products/${product.id}`).then((response) => {
          console.log("Successfully destroyed", response.data);
          window.alert("Successfully destroyed. Nice.");
          var index = this.products.indexOf(product);
          this.products.splice(index, 1);
        });
      }
    },
  },
};
</script>
