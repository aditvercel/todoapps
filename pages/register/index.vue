<template>
  <div
    :style="{
      display: 'flex',
      justifyContent: 'space-between',
      height: '100%',
      flexDirection: signInPages
        ? 'row-reverse'
        : 'row' /* Reverse flex direction when signInPages is true */,
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
            <v-text-field
              v-model="name"
              :counter="10"
              :rules="nameRules"
              label="Full name"
              required
            ></v-text-field>
            <v-text-field
              v-model="email"
              :rules="emailRules"
              label="E-mail"
              required
            ></v-text-field>
            <v-text-field
              v-model="password"
              label="Password"
              required
            ></v-text-field>
            <div style="margin-top: 20px; display: flex; justify-content: end">
              <v-btn
                outlined
                rounded-xl
                @click="() => (this.signInPages = true)"
              >
                Register
              </v-btn>
            </div>
          </v-container>
        </v-form>
      </div>

      <!-- Login Form shown when signInPages is true -->
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
              v-model="email"
              label="E-mail"
              required
            ></v-text-field>
            <v-text-field
              v-model="password"
              label="Password"
              required
            ></v-text-field>
            <div style="margin-top: 20px; display: flex; justify-content: end">
              <v-btn outlined rounded-xl :to="'/todoPages'"> Sign In </v-btn>
            </div>
          </v-container>
        </v-form>
      </div>
    </div>
  </div>
</template>
<style scoped></style>

<script>
export default {
  name: "HomePage",
  data: () => ({
    valid: false,
    signInPages: false,
    name: "",
    lastname: "",
    password: "",
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
    // Check the query parameter in the URL and set signInPages accordingly
    if (this.$route.query.login === "true") {
      this.signInPages = true;
    }
  },
  methods: {
    toggleSignInPage() {
      this.signInPages = !this.signInPages;
    },
  },
};
</script>
