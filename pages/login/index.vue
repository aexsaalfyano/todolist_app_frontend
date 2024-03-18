<script lang="ts">
import axios from 'axios';
import { defineComponent, ref } from 'vue';

export default defineComponent({
  setup() {
    const config = useRuntimeConfig();
    const email = ref('');
    const password = ref('');
    const errorMessage = ref('');
    //init router
    const router = useRouter();
    
    const login = async () => {
      try {
        const response = await axios.post(`${config.public.apiBase}/api/login`, {
          email: email.value,
          password: password.value
        });

        if (response.status === 200 && response.data.meta.status === true) {
          localStorage.setItem('token', response.data.data.access_token.token);

          router.push('/');
        } else {
          errorMessage.value = response.data.meta.message || 'Login failed';
        }
      } catch (error) {
        console.error('Error during login:', error);
        errorMessage.value = 'An unexpected error occurred';
      }
    };

    return {
      email,
      password,
      errorMessage,
      login
    };
  }
});
</script>

<template>
  <div class="container mt-5">
    <div class="row justify-content-center">
      <div class="col-md-6">
        <div class="card">
          <div class="card-header">Login</div>
          <div class="card-body">
            <form @submit.prevent="login">
              <div class="mb-3">
                <label for="email" class="form-label">Email address</label>
                <input type="email" class="form-control" id="email" v-model="email" required>
              </div>
              <div class="mb-3">
                <label for="password" class="form-label">Password</label>
                <input type="password" class="form-control" id="password" v-model="password" required>
              </div>
              <button type="submit" class="btn btn-primary">Login</button>
            </form>
            <div v-if="errorMessage" class="alert alert-danger mt-3">{{ errorMessage }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>



<style>
/* Tambahkan CSS tambahan jika diperlukan */
</style>
