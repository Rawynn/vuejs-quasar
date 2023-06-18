<template>
  <div>
    <ul>
      <li v-for="user in filteredUsers" :key="user.id">
        <q-avatar :src="user.avatar"></q-avatar>
        <router-link :to="{ name: 'UserDetails', params: { userId: user.id } }">
          {{ user.first_name }} {{ user.last_name }}
        </router-link>
        <favorite-button :user-id="user.id"></favorite-button>
      </li>
    </ul>
  </div>
</template>

<script>
import FavoriteButton from "@/components/FavoriteButton.vue";

export default {
  components: {
    FavoriteButton,
  },
  props: {
    users: {
      type: Array,
      required: true,
    },
    searchQuery: {
      type: String,
      required: true,
    },
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
  },
};
</script>

<style scoped>
.cursor-pointer {
  cursor: pointer;
}
</style>
