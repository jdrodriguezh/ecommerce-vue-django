<template>
  <div class="page-sign-up">
    <div class="columns">
      <div class="column is-4 is-offset-4">
        <h1 class="title">Sign up</h1>
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
          <div class="field">
            <label>Confirm Password</label>
            <div class="control">
              <input type="password" class="input" v-model="confirmPassword" />
            </div>
          </div>
          <div class="notification is-danger" v-if="errors.length">
            <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
          </div>
          <div class="field">
            <div class="control">
              <button class="button is-dark">Sign Up</button>
            </div>
          </div>
          <hr />
          Or <router-link to="/log-in">click here</router-link> to log in!
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
import { toast } from "bulma-toast";

export default {
  name: "SignUp",
  data() {
    return {
      username: "",
      password: "",
      confirmPassword: "",
      errors: [],
    };
  },
  mounted() {
    document.title = "Sign Up | Djackets";
  },
  methods: {
    submitForm() {
      this.errors = [];
      if (!this.username) {
        this.errors.push("The username is missing");
      }
      if (!this.password) {
        this.errors.push("The password is too short");
      }
      if (this.password !== this.confirmPassword) {
        this.errors.push(`The passwords don't match`);
      }

      if (!this.errors.length) {
        const payload = {
          username: this.username,
          password: this.password,
        };

        axios
          .post("/api/v1/users/", payload)
          .then((resp) => {
            toast({
              message: "Account created, please log in!",
              type: "is-success",
              dismissible: true,
              pauseOnHover: true,
              duration: 2000,
              position: "bottom-right",
            });
            this.$router.push("/log-in");
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