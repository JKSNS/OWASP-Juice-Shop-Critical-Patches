<template>
  <div class="form-container">
    <h2>Login</h2>

    <form @submit.prevent="loginUser" class="form">
      <div>
        <label for="username">Username:</label>
        <input type="username" id="username" v-model="username" required />
      </div>
      <div>
        <label for="password">Password:</label>
        <input type="password" id="password" v-model="password" required />
      </div>
      <button type="submit" class="form-btn" :disabled="isLoading">
        {{ isLoading ? "Logging in..." : "Login" }}
      </button>
    </form>

    <p class="success-message" v-if="successMessage">{{ successMessage }}</p>
    <p class="error-message" v-if="errorMessage">{{ errorMessage }}</p>
  </div>
</template>

<script>
import { mapActions } from "vuex";
import jwtDecode from "jwt-decode";
import { useRouter } from "vue-router";

export default {
  data() {
    return {
      username: "",
      password: "",
      isLoading: false,
      successMessage: "",
      errorMessage: "",
    };
  },

  setup() {
    const router = useRouter();
    return { router };
  },

  methods: {
    ...mapActions([
      "setAccessToken",
      "setLoggedIn",
      "addToCart",
      "clearCart",
    ]),

    validateInput() {
      // Basic validation rules
      if (!this.username.match(/^[a-zA-Z0-9_.-]*$/)) {
        this.errorMessage = "Invalid username format";
        return false;
      }
      if (this.password.length < 6) { // Example: minimum 6 characters
        this.errorMessage = "Password must be at least 6 characters";
        return false;
      }
      return true;
    },

    async loginUser() {
      this.isLoading = true;
      this.successMessage = "";
      this.errorMessage = "";
      
      if (!this.validateInput()) {
        this.isLoading = false;
        return;
      }

      try {
        const response = await fetch(
          `${process.env.VUE_APP_CLIENT_ORIGIN}/api/users/login`,
          {
            method: "POST",
            headers: {
              "Content-Type": "application/json",
            },
            body: JSON.stringify({
              username: this.username,
              password: this.password,
            }),
          }
        );

        // ... rest of the loginUser method ...

      } catch (error) {
        this.errorMessage = error.message;
      } finally {
        this.isLoading = false;
      }
    },
  },
};
</script>

<style scoped src="../styles/login-form.css"></style>
