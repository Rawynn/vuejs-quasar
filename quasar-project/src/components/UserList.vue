<template>
  <ul class="user-list">
    <li class="list-element" v-for="user in filteredUsers" :key="user.id">
      <div class="avatar-name-holder">
        <q-avatar class="list-avatar" :src="user.avatar">
          <q-img :src="user.avatar" :alt="user.first_name"></q-img>
        </q-avatar>
        <router-link :to="{ name: 'UserDetails', params: { userId: user.id } }">
          {{ user.first_name }} {{ user.last_name }}
        </router-link>
      </div>

      <favorite-button
        class="list-favorite"
        :user-id="user.id"
      ></favorite-button>
    </li>
  </ul>
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
