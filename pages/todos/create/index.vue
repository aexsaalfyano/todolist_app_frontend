<script setup lang="ts">
import axios from 'axios';
import { useRouter } from 'vue-router';
import { ref } from 'vue';

useHead({
    title: 'Create a Todo',
});

const config = useRuntimeConfig();  

const router = useRouter();

const title = ref('');
const description = ref('');
const errors = ref<{ title: string[]; description: string[] }>({ title: [], description: [] });
const token = ref<string | null>(null);


const storeTodo = async () => {
    let formData = new FormData();

    formData.append('title', title.value);
    formData.append('description', description.value);

    try {
        const token = localStorage.getItem('token');

        await axios.post(`${config.public.apiBase}/api/todo`, formData, {
            headers: {
                Authorization: `Bearer ${token}`
            }
        });

        router.push({ path: "/todos" });
    } catch (error: any) {
        const responseData = error.response.data;
        errors.value.title = responseData.title || ['An error occurred'];
        errors.value.description = responseData.description || ['An error occurred'];

        localStorage.removeItem('token');
        token.value = null;
        router.push('/login');
    }
}
</script>

<template>
  <div class="container mt-5">
      <div class="row">
          <div class="col-md-12">
              <div class="card border-0 rounded shadow">
                  <div class="card-body">
                      <form @submit.prevent="storeTodo()">
                          <div class="mb-3">
                              <label class="form-label fw-bold">Title</label>
                              <input type="text" class="form-control" v-model="title" placeholder="Title Todo">
                              <div v-if="errors.title.length > 0" class="alert alert-danger mt-2">
                                  <span>{{ errors.title[0] }}</span>
                              </div>
                          </div>
                          <div class="mb-3">
                              <label class="form-label fw-bold">Description</label>
                              <textarea class="form-control" v-model="description" rows="5" placeholder="Description Todo"></textarea>
                              <div v-if="errors.description.length > 0" class="alert alert-danger mt-2">
                                  <span>{{ errors.description[0] }}</span>
                              </div>
                          </div>
                          <button type="submit" class="btn btn-md btn-primary rounded-sm shadow border-0">Save</button>
                      </form>
                  </div>
              </div>
          </div>
      </div>
  </div>
</template>
