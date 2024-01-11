<template>
  <div id="products">
    <h1>Product List</h1>
    <div class="product-grid">
      <div class="product-card" v-for="product in products" :key="product.id" @click="showDescription(product)">
        <h2 class="product-title">{{ product.name }}</h2>
        <p class="product-price">${{ product.price }}</p>
        <img class="product-image" :src="require('@/assets/products/' + product.image_url)" alt="Product Image" />
        <button v-if="isLoggedIn" class="add-basket-btn" @click.stop="addToBasket(product.id)">
          Add to Basket
        </button>

        <div v-if="selectedProduct === product" class="overlay">
          <div class="overlay-content">
            <img class="product-image-overlay" :src="require('@/assets/products/' + product.image_url)"
              alt="Product Image" />
            <p class="product-description">{{ product.description }}</p>
            <p v-if="isLoggedIn">
              <ReviewForm :productId="selectedProduct.id" :userId="userId" />
            </p>
            <p v-if="itemAdded" class="confirmation-message">
              {{ confirmationMessage }}
            </p>
            <button class="close-button" @click.stop="selectedProduct = null">
              Close
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from "vuex";
import ReviewForm from "../components/ReviewForm.vue";
import { useRouter } from "vue-router";

export default {
  components: {
    ReviewForm,
  },

  data() {
    return {
      products: [],
      selectedProduct: null,
      quantity: {},
      itemAdded: false,
      confirmationMessage: "",
    };
  },

  computed: {
    ...mapGetters(["isLoggedIn", "userId", "cartCount"]),
  },

  methods: {
    ...mapActions(["addToCart"]),

    showDescription(product) {
      this.selectedProduct = product;
    },

    async addToBasket(productId) {
      // existing addToBasket method...
    },

    handleOutsideClick(event) {
      // existing handleOutsideClick method...
    },
  },

  async mounted() {
    if (!this.isLoggedIn) {
      const router = useRouter();
      router.push({ name: 'login' });
      return;
    }

    const response = await fetch(`${process.env.VUE_APP_CLIENT_ORIGIN}/api/products/listAll`);
    this.products = await response.json();
    this.products.forEach((product) => {
      this.quantity[product.id] = 1;
    });
  },
};
</script>

<style scoped src="../styles/product-list.css"></style>
