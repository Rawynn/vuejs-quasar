<template>
  <div class="q-pa-md">
    <h1>User</h1>

    <q-input
      outlined
      dense
      v-model="searchQuery"
      placeholder="Search..."
      @input="searchUsers"
    ></q-input>

    <user-list :users="filteredUsers" :search-query="searchQuery"></user-list>

    <q-pagination
      v-model="currentPage"
      :min="1"
      :max="totalPages"
      :input="true"
      @input="fetchUsers"
    ></q-pagination>
  </div>
</template>

<script>
import axios from "axios";
import UserList from "@/components/UserList.vue";

export default {
  components: {
    UserList,
  },
  data() {
    return {
      users: [],
      currentPage: 1,
      perPage: 0,
      totalUsers: 0,
      cache: new Map(),
      searchQuery: "",
      favorites: [],
    };
  },
  computed: {
    filteredUsers() {
      if (this.searchQuery) {
        const query = this.searchQuery.toLowerCase();
        return this.users.filter((user) => {
          const fullName = `${user.first_name} ${user.last_name}`.toLowerCase();
          return fullName.includes(query);
        });
      } else {
        return this.users;
      }
    },
    totalPages() {
      return Math.ceil(this.totalUsers / this.perPage);
    },
  },
  mounted() {
    this.fetchUsers();
    this.loadFavorites();
  },
  methods: {
    fetchUsers() {
      const cacheKey = `${this.currentPage}-${this.searchQuery}`;
      if (this.cache.has(cacheKey)) {
        this.users = this.cache.get(cacheKey);
        this.totalUsers = this.cache.get("total");
        this.perPage = this.cache.get("perPage");
      } else {
        // Clear the cache if the search query or page has changed
        if (
          this.searchQuery !== this.cache.get("searchQuery") ||
          this.currentPage !== this.cache.get("currentPage")
        ) {
          this.cache.clear();
        }

        axios
          .get(`https://reqres.in/api/users?page=${this.currentPage}`, {
            params: {
              page: this.currentPage, // Update the page parameter
              per_page: this.perPage,
              q: this.searchQuery,
            },
          })
          .then((response) => {
            this.users = response.data.data;
            this.totalUsers = response.data.total;
            this.perPage = response.data.per_page;

            // Adjust the current page if it exceeds the total pages after search
            const totalPages = Math.ceil(this.totalUsers / this.perPage);
            if (this.currentPage > totalPages) {
              this.currentPage = totalPages;
            }

            // Update the cache
            this.cache.set(cacheKey, this.users);
            this.cache.set("total", this.totalUsers);
            this.cache.set("perPage", this.perPage);
            this.cache.set("searchQuery", this.searchQuery);
            this.cache.set("currentPage", this.currentPage);
          })
          .catch((error) => {
            console.error(error);
          });
      }
    },

    searchUsers() {
      this.currentPage = 1;
      this.fetchUsers();
    },
    isFavorite(userId) {
      return this.favorites.includes(userId);
    },
    toggleFavorite(userId) {
      if (this.isFavorite(userId)) {
        this.favorites = this.favorites.filter((id) => id !== userId);
      } else {
        this.favorites.push(userId);
      }
      this.saveFavorites();
    },
    saveFavorites() {
      localStorage.setItem("favorites", JSON.stringify(this.favorites));
    },
    loadFavorites() {
      const favorites = localStorage.getItem("favorites");
      if (favorites) {
        this.favorites = JSON.parse(favorites);
      }
    },
  },
  watch: {
    currentPage() {
      if (this.currentPage > this.totalPages) {
        this.currentPage = this.totalPages;
      } else {
        this.fetchUsers();
      }
    },
  },
};
</script>
