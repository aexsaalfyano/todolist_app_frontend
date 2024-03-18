<script setup lang="ts">
import axios from 'axios';
import { useRouter, useRoute } from 'vue-router';
import { ref } from 'vue';

useHead({
    title: 'Edit a Todo',
});

const config = useRuntimeConfig();  

const router = useRouter();

const route = useRoute();

const title = ref('');
const description = ref('');
const errors = ref<{ title?: string[]; description?: string[] }>({});

const fetchTodoData = async () => {
    try {
        const response = await axios.get(`${config.public.apiBase}/api/todo/${route.params.id}`, {
            headers: {
                Authorization: `Bearer ${localStorage.getItem('token')}`
            }
        });
        console.log(response);
        const data = response.data.data;
        title.value = data.todo.title;
        description.value = data.todo.description;
    } catch (error: any) {
        console.error('Error fetching todo data:', error);
    }
}

onMounted(fetchTodoData);

const updateTodo = async () => {
    try {
        const data = {
            title: title.value,
            description: description.value,
        };

        await axios.put(`${config.public.apiBase}/api/todo/${route.params.id}`, data, {
            headers: {
                Authorization: `Bearer ${localStorage.getItem('token')}`
            }
        });

        router.push({ path: "/todos" });
    } catch (error: any) {
        if (error.response && error.response.data) {
            const responseData = error.response.data;
            errors.value.title = responseData.title || ['An error occurred'];
            errors.value.description = responseData.description || ['An error occurred'];
        }
    }
}
</script>


<template>
    <div class="container mt-5">
        <div class="row">
            <div class="col-md-12">
                <div class="card border-0 rounded shadow">
                    <div class="card-body">
                        <form @submit.prevent="updateTodo()">
                           
                            <div class="mb-3">
                                <label class="form-label fw-bold">Title</label>
                                <input type="text" class="form-control" v-model="title" placeholder="Title Todo">
                                <div v-if="errors.title" class="alert alert-danger mt-2">
                                    <span>{{ errors.title[0] }}</span>
                                </div>
                            </div>
                            <div class="mb-3">
                                <label class="form-label fw-bold">Description</label>
                                <textarea class="form-control" v-model="description" rows="5" placeholder="Description Todo"></textarea>
                                <div v-if="errors.description" class="alert alert-danger mt-2">
                                    <span>{{ errors.description[0] }}</span>
                                </div>
                            </div>
                            <button type="submit" class="btn btn-md btn-primary rounded-sm shadow border-0">Update</button>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
