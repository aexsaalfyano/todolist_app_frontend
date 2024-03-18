<script setup lang="ts">
import { watchEffect, ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';

const token = ref<string | null>(null);
const router = useRouter();
const config = useRuntimeConfig();

watchEffect(() => {
    if (process.client) {
        const storedToken = localStorage.getItem('token');
        if (!storedToken) {
            router.push('/login');
        } else {
            token.value = storedToken;
        }
    }
});

const logout = async () => {
    try {
        const confirmed = window.confirm('Are you sure you want to delete this todo?');

        if (confirmed) {
            await axios.post(`${config.public.apiBase}/api/logout`, {}, {
                headers: {
                    Authorization: `Bearer ${localStorage.getItem('token')}`
                }
            });
            
            localStorage.removeItem('token');
            token.value = null;
            router.push('/login');
        }
    } catch (error) {
        // console.error('Error logging out:', error);
        localStorage.removeItem('token');
        token.value = null;
        router.push('/login');
    }
}
</script>



<template>
  <div>
      <nav class="navbar navbar-expand-lg bg-body-tertiary" style="background-color: #e3f2fd;">
          <div class="container">
              <NuxtLink class="navbar-brand" to="/">HOME</NuxtLink>
              <button class="navbar-toggler" type="button" data-bs-toggle="collapse"
                  data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false"
                  aria-label="Toggle navigation">
                  <span class="navbar-toggler-icon"></span>
              </button>
              <div class="collapse navbar-collapse" id="navbarSupportedContent">
                  <ul class="navbar-nav me-auto mb-2 mb-lg-0" v-if="token">
                      <li class="nav-item">
                          <NuxtLink class="nav-link active" aria-current="page" to="/todos">TODO LIST</NuxtLink>
                      </li>
                  </ul>
                  <ul class="navbar-nav ms-auto mb-2 mb-lg-0" role="search">
                    <button v-if="token" class="btn btn-primary" @click="logout">Logout</button>
                  </ul>
              </div>
          </div>
      </nav>

      <!--- render page -->
      <slot />

  </div>
</template>

<style scoped>
body { background-color: lightgray; font-family: 'Quicksand', sans-serif }
</style>
