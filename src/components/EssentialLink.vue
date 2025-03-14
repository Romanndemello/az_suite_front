<template>
  <q-item
    clickable
    class="bg-primary text-white"
    no-caps
    style="border-radius: 8px;"
    @click="$router.push(route)"
  >
    <q-item-section
      avatar
      class="text-white"
      style="min-width: 32px;"
    >
      <q-img
        :src="icon"
        class="custom-icon"
      />
    </q-item-section>

    <q-item-section>
      <q-item-label class="text-left">{{ title }}</q-item-label>
    </q-item-section>
  </q-item>
</template>

<script setup lang="ts">

defineOptions({
  name: 'EssentialLink'
});

export interface EssentialLinkProps {
  title: string;
  route?: string;
  icon?: string;
};

withDefaults(defineProps<EssentialLinkProps>(), {
  icon: '',
  route: '/'
});
</script>
