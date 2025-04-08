<template>
  <div class="px-8">
    
  </div>
</template>

<script setup>
import { useAuthStore, useUsersStore } from "../store";
import { computed, onMounted } from "vue";

const authStore = useAuthStore();
const usersStore = useUsersStore();

onMounted(() => {
  usersStore.getAll();
});

const user = computed(() => {
  return JSON.parse(authStore.user);
});

const users = computed(() => {
  return usersStore.users;
});
usersStore.getAll();
</script>
