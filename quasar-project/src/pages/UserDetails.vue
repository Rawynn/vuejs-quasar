<template>
  <div class="q-pa-md">
    <h1>User Details</h1>
    <div v-if="user">
      <q-avatar>
        <q-img
          :src="user.avatar"
          :alt="user.first_name"
          class="user-avatar"
        ></q-img>
      </q-avatar>
      <h2>{{ user.first_name }} {{ user.last_name }}</h2>
      <p>Email: {{ user.email }}</p>
      <favorite-button :user-id="user.id"></favorite-button>
    </div>
    <div v-else>
      <p>Loading user details...</p>
    </div>
    <q-btn color="primary" label="Back to List" @click="goBackToList" />
  </div>
</template>

<script>
import axios from "axios";
import FavoriteButton from "@/components/FavoriteButton.vue";

export default {
  components: {
    FavoriteButton,
  },
  props: ["userId"],
  data() {
    return {
      user: null,
    };
  },
  mounted() {
    this.fetchUser();
  },
  methods: {
    fetchUser() {
      axios
        .get(`https://reqres.in/api/users/${this.userId}`)
        .then((response) => {
          this.user = response.data.data;
        })
        .catch((error) => {
          console.error(error);
        });
    },
    isFavorite(userId) {
      const favorites = localStorage.getItem("favorites");
      if (favorites) {
        return JSON.parse(favorites).includes(userId);
      }
      return false;
    },
    toggleFavorite(userId) {
      const favorites = localStorage.getItem("favorites");
      if (favorites) {
        let favoriteList = JSON.parse(favorites);
        if (favoriteList.includes(userId)) {
          favoriteList = favoriteList.filter((id) => id !== userId);
        } else {
          favoriteList.push(userId);
        }
        localStorage.setItem("favorites", JSON.stringify(favoriteList));
      } else {
        localStorage.setItem("favorites", JSON.stringify([userId]));
      }
    },
    goBackToList() {
      this.$router.push({ path: "/" });
    },
  },
};
</script>

<style>
.cursor-pointer {
  cursor: pointer;
}
</style>
