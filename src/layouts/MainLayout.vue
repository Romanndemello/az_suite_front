<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="bg-secondary text-accent justify-end">
        <div 
          class="q-pa-md text-right text-caption"
        >
          Olá,
          <p 
            class="no-margin text-bold text-subtitle1" 
            style="line-height: 90%;"
          >
            Darta
          </p>
        </div>
        <q-avatar 
          color="primary" 
        >
          <q-img 
            :src="iconProfile"
            style="width: 40%;"
          />
        </q-avatar>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      elevated
      bordered
    >
      <q-list class="q-gutter-y-md q-py-md text-center q-mx-md">
        <q-item-label
          header
          class="text-center"
        >
          <q-img
            src="../assets/logo.png"
            style="width: 40%"
          />
        </q-item-label>

        <EssentialLink
          v-for="item in pagesList"
          :key="item.title"
          v-bind="item"
        />
      </q-list>
    </q-drawer>

    <q-page-container class="bg-info">
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import EssentialLink, { EssentialLinkProps } from 'components/EssentialLink.vue';
import iconDashboard from '../assets/icon_dashboard.png';
import iconProfile from '../assets/icon_profile.png';

defineOptions({
  name: 'MainLayout'
});

const pagesList: EssentialLinkProps[] = [
  {
    title: 'Dashboard',
    icon: iconDashboard,
    route: 'dashboard'
  }
];

const leftDrawerOpen = ref(false);

function toggleLeftDrawer () {
  leftDrawerOpen.value = !leftDrawerOpen.value;
}
</script>
