<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>User list VueJS + Quasar + ReqRes</q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" :show-if-above="false" bordered>
      <q-list>
        <q-item-label header> Menu </q-item-label>

        <MainMenu v-for="link in mainMenu" :key="link.title" v-bind="link" />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from "vue";
import MainMenu from "@/components/MainMenu.vue";

const linksList = [
  {
    title: "Home",
    caption: "Return to home page",
    icon: "public",
    link: "/",
  },
];

export default defineComponent({
  name: "MainLayout",

  components: {
    MainMenu,
  },

  setup() {
    const leftDrawerOpen = ref(false);

    return {
      mainMenu: linksList,
      leftDrawerOpen,
      toggleLeftDrawer() {
        leftDrawerOpen.value = !leftDrawerOpen.value;
      },
    };
  },
});
</script>
