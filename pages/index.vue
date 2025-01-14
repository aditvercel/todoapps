<template>
  <div style="display: grid; height: 100%; background-color: white; gap: 10px">
    <div
      style="
        width: 100%;
        height: 50px;
        background-color: white;
        display: flex;
        justify-content: center;
        align-items: center;
        margin-top: 20px;
      "
    >
      <div style="display: flex; gap: 30px">
        <div v-for="(item, index) in navigationData" :key="index">
          <NuxtLink
            :to="item.link"
            style="cursor: pointer; text-decoration: none; color: black"
          >
            {{ item.title }}
          </NuxtLink>
        </div>
      </div>
    </div>
    <div
      style="
        height: 100%;
        width: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
      "
    >
      <v-card
        elevation-6
        style="
          padding: 10px;
          min-height: 100vw;
          width: 94%;
          background-color: #f7f7f7;
          display: flex;
          justify-content: center;
        "
      >
        <div>
          <div
            style="
              font-weight: 500;
              font-size: 50px;
              justify-content: center;
              display: flex;
            "
          >
            <div style="width: 600px; text-align: center">
              Unleash And Transform Your Daily Habbit
            </div>
          </div>
          <div>
            <div
              style="
                font-size: 12px;
                justify-content: center;
                display: flex;
                margin-top: 20px;
              "
            >
              <div style="width: 600px; text-align: center">
                Consequat proident mollit commodo amet amet in officia quis
                mollit qui in aliqua officia. Amet velit eu et laborum laborum.
                Veniam ullamco deserunt aute culpa eiusmod. Exercitation elit
                qui do sint eiusmod Lorem.
              </div>
            </div>
            <div
              style="
                display: flex;
                justify-content: center;
                margin-top: 50px;
                margin-bottom: 50px;
              "
            >
              <v-btn color="primary" rounded-xl to="/register">
                Get Started
              </v-btn>
            </div>
          </div>
          <div>
            <img src="/public/images/todoimages.png" alt="My Image" />
          </div>
        </div>
      </v-card>
    </div>
  </div>
</template>

<script>
import navigationData from "../data/navigation.js";

export default {
  name: "HomePage",
  data() {
    return {
      navigationData,
    };
  },
};
</script>

<style scoped></style>
