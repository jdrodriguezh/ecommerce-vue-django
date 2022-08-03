<template>
  <div class="page-sign-up">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h1 class="title">Log In</h1>
        <form @submit.prevent="submitForm">
          <div class="field">
            <label>Username</label>
            <div class="control">
              <input type="text" class="input" v-model="username" />
            </div>
          </div>
          <div class="field">
            <label>Password</label>
            <div class="control">
              <input type="password" class="input" v-model="password" />
            </div>
          </div>
          <div class="notification is-danger" v-if="errors.length">
            <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
          </div>
          <div class="field">
            <div class="control">
              <button class="button is-dark">Log In</button>
            </div>
          </div>
          <hr />
          Or <router-link to="/sign-up">click here</router-link> to sign up!
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import { toast } from "bulma-toast";

export default {
  name: "LogIn",
  data() {
    return {
      username: "",
      password: "",
      errors: [],
    };
  },
  mounted() {
    document.title = "Log In | Djackets";
  },
  methods: {
    async submitForm() {
      this.errors = [];
      if (!this.username) {
        this.errors.push("The username is missing");
      }
      if (!this.password) {
        this.errors.push("The password is too short");
      }

      if (!this.errors.length) {
        const payload = {
          username: this.username,
          password: this.password,
        };

        await axios
          .post("/api/v1/token/login", payload)
          .then((resp) => {
            const token = resp.data.auth_token;
            this.$store.commit("setToken", token);
            axios.defaults.headers.common["Authorization"] = `Token ${token}`;
            localStorage.setItem("token", token);
            const toPath = this.$route.query.to || '/cart'
            this.$router.push(toPath);
          })
          .catch((err) => {
            if (err.response) {
              err.response.data.forEach((e) => {
                this.errors.push(`${e}: ${err.response.data[e]}`);
              });
              console.log(JSON.stringify(err.response.data));
            } else if (err.message) {
              this.errors.push("Something went wrong. Please try again");
              console.log(JSON.stringify(err));
            }
          });
      }
    },
  },
};
</script>