<template>
  <div
    :style="{
      display: 'flex',
      justifyContent: 'space-between',
      height: '100%',
      flexDirection: signInPages ? 'row-reverse' : 'row',
    }"
  >
    <div
      style="
        width: 900px;
        background-color: #3eb796;
        justify-content: center;
        align-items: center;
        display: flex;
      "
    >
      <router-link
        to="/"
        style="
          all: unset;
          position: absolute;
          top: 20px;
          left: 20px;
          text-decoration: none;
        "
      >
        <button style="all: unset; cursor: pointer">
          <h2>Home</h2>
        </button>
      </router-link>

      <div>
        <div style="color: white; font-weight: 500; font-size: 50px">
          Welcome Back
        </div>
        <div style="color: white">
          To keep connected with us please login...
        </div>
        <div style="margin-top: 20px">
          <v-btn outlined rounded-xl @click="toggleSignInPage">
            {{ signInPages ? "Register" : "SignIn" }}
          </v-btn>
        </div>
      </div>
    </div>

    <div
      style="
        width: 100%;
        justify-content: center;
        align-items: center;
        display: flex;
      "
    >
      <div v-if="!signInPages" style="display: grid">
        <div
          style="
            color: #79c5ba;
            text-align: center;
            font-weight: 500;
            font-size: 30px;
            margin-bottom: 20px;
          "
        >
          Create Account
        </div>
        <v-form v-model="valid">
          <v-container style="min-width: 500px">
            <!-- <v-text-field
              v-model="name"
              :counter="10"
              :rules="nameRules"
              label="Full name"
              required
            ></v-text-field> -->
            <v-text-field
              v-model="email"
              :rules="emailRules"
              label="E-mail"
              type="email"
              required
            ></v-text-field>
            <v-text-field
              v-model="registerUsername"
              label="Username"
              required
            ></v-text-field>
            <v-text-field
              v-model="registerPassword"
              label="Password"
              type="password"
              required
            ></v-text-field>
            <div style="margin-top: 20px; display: flex; justify-content: end">
              <v-btn outlined rounded-xl @click="registerUserBaru()">
                Register
              </v-btn>
            </div>
          </v-container>
        </v-form>
      </div>

      <div
        v-if="signInPages"
        style="display: grid; width: 400px; text-align: center"
      >
        <div
          style="
            color: #79c5ba;
            font-weight: 500;
            font-size: 30px;
            margin-bottom: 20px;
          "
        >
          Login
        </div>
        <v-form v-model="valid">
          <v-container style="min-width: 500px">
            <v-text-field
              v-model="username"
              label="Username"
              required
            ></v-text-field>
            <v-text-field
              v-model="password"
              label="Password"
              type="password"
              required
            ></v-text-field>
            <div style="margin-top: 20px; display: flex; justify-content: end">
              <v-btn outlined rounded-xl @click="login()"> Sign In </v-btn>
            </div>
          </v-container>
        </v-form>
      </div>
    </div>
  </div>
  <v-snackbar v-model="snackbar">
    {{ this.responseFeedback }}

    <template v-slot:actions>
      <v-btn color="pink" variant="text" @click="snackbar = false">
        Close
      </v-btn>
    </template>
  </v-snackbar>
</template>
<style scoped></style>

<script>
import axios from "axios";
import Cookies from "js-cookie";
export default {
  name: "HomePage",
  data: () => ({
    url: "http://94.74.86.174:8080/api",
    valid: false,
    signInPages: false,
    name: "",
    lastname: "",
    password: "",
    username: "",
    snackbar: false,
    responseFeedback: "",
    registerPassword: "",
    registerUsername: "",
    nameRules: [
      (value) => {
        if (value) return true;

        return "Name is required.";
      },
      (value) => {
        if (value?.length <= 10) return true;

        return "Name must be less than 10 characters.";
      },
    ],
    email: "",
    emailRules: [
      (value) => {
        if (value) return true;

        return "E-mail is required.";
      },
      (value) => {
        if (/.+@.+\..+/.test(value)) return true;

        return "E-mail must be valid.";
      },
    ],
  }),
  created() {
    if (this.$route.query.login === "true") {
      this.signInPages = true;
    }
  },
  methods: {
    toggleSignInPage() {
      this.signInPages = !this.signInPages;
    },

    async registerUserBaru() {
      try {
        let res = await axios.post(`${this.url}/register`, {
          email: this.email,
          username: this.registerUsername,
          password: this.registerPassword,
        });

        console.log(
          this.email,
          this.registerUsername,
          this.registerPasswordpassword
        );

        if (res) {
          this.snackbar = true;
          this.responseFeedback = res.data.message || res.data.errorMessage;
          if (res.status === 200) {
            this.signInPages = !this.signInPages;
          }
        }
      } catch (error) {
        console.log(error);
      }
    },

    async login() {
      try {
        let res = await axios.post(`${this.url}/login`, {
          // email: this.email,
          username: this.username,
          password: this.password,
        });

        if (res) {
          this.snackbar = true;
          this.responseFeedback = res.data.message || res.data.errorMessage;
          console.log(res);
          if (res.status === 200) {
            Cookies.set("authToken", res.data.data.token, { expires: 1 });
            this.$router.push("/todopages");
          }
        }
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>
