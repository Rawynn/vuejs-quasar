<template>
  <q-icon
    :name="isFavorite ? 'star' : 'star_border'"
    @click="toggleFavorite"
    class="cursor-pointer star"
  ></q-icon>
</template>

<script>
export default {
  props: {
    userId: {
      type: Number,
      required: true,
    },
  },
  data() {
    return {
      isFavorite: false,
    };
  },
  mounted() {
    this.isFavorite = this.checkFavorite(this.userId);
  },
  methods: {
    checkFavorite(userId) {
      const favorites = localStorage.getItem("favorites");
      if (favorites) {
        return JSON.parse(favorites).includes(userId);
      }
      return false;
    },
    toggleFavorite() {
      const favorites = localStorage.getItem("favorites");
      if (favorites) {
        let favoriteList = JSON.parse(favorites);
        if (favoriteList.includes(this.userId)) {
          favoriteList = favoriteList.filter((id) => id !== this.userId);
        } else {
          favoriteList.push(this.userId);
        }
        localStorage.setItem("favorites", JSON.stringify(favoriteList));
      } else {
        localStorage.setItem("favorites", JSON.stringify([this.userId]));
      }
      this.isFavorite = !this.isFavorite;
    },
  },
};
</script>
