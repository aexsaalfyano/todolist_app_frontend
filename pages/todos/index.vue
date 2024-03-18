<script setup lang="ts">
import axios from 'axios';
import { ref, onMounted } from 'vue';
import { useRouter } from 'vue-router';

const todos = ref<{ id: number; title: string; description: string; status: string;}[]>([]);
const config = useRuntimeConfig();
const router = useRouter();
const token = ref<string | null>(null);

const fetchData = async () => {
    try {
        const token = localStorage.getItem('token');
        const response = await axios.get(`${config.public.apiBase}/api/todos`, {
            headers: {
                Authorization: `Bearer ${token}`
            }
        });
        todos.value = response.data.data.todos; 
    } catch (error) {
        // console.error('Error fetching data:', error);
        localStorage.removeItem('token');
        token.value = null;
        router.push('/login');
    }
};

const deleteTodo = async (id: number) => {
    try {
        const confirmed = window.confirm('Are you sure you want to delete this todo?');
        if (confirmed) {
            const token = localStorage.getItem('token');
            await axios.delete(`${config.public.apiBase}/api/todo/${id}`, {
                headers: {
                    Authorization: `Bearer ${token}`
                }
            });
            todos.value = todos.value.filter(todo => todo.id !== id);
        }
    } catch (error: any) {
        console.error('Error deleting todo:', error);
    }
};

const toggleStatus = async (todo: { id: number; status: string }) => {
    try {
        const token = localStorage.getItem('token');
        
        const updatedStatus = todo.status === 'completed' ? 'progress' : 'completed';
        await axios.put(`${config.public.apiBase}/api/todo/${todo.id}`, { status: updatedStatus }, {
            headers: {
                Authorization: `Bearer ${token}`
            }
        });
        todo.status = updatedStatus;
    } catch (error: any) {
        // console.error('Error updating status:', error);
        localStorage.removeItem('token');
        token.value = null;
        router.push('/login');
    }
};

onMounted(fetchData);
</script>

<template>
    <div class="container mt-5 mb-5">
        <div class="row">
            <div class="col-md-12">
                <NuxtLink to="/todos/create" class="btn btn-md btn-primary rounded shadow border-0 mb-3">ADD NEW TODOS</NuxtLink>

                <div class="card border-0 rounded shadow">
                    <div class="card-body">
                        <table class="table table-bordered">
                            <thead class="bg-success text-white">
                                <tr>
                                    <th scope="col" width="10px">#</th>
                                    <th scope="col">Title</th>
                                    <th scope="col">Description</th>
                                    <th scope="col">Status</th>
                                    <th scope="col" style="width:15%">Actions</th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(todo, index) in todos" :key="index">
                                    <td><input type="checkbox" :checked="todo.status === 'completed'" @change="toggleStatus(todo)"></td>
                                    <td>{{ todo.title }}</td>
                                    <td>{{ todo.description }}</td>
                                    <td>{{ todo.status }}</td>
                                    <td class="text-center">
                                        <NuxtLink :to="`/todos/edit/${todo.id}`" class="btn btn-sm btn-primary rounded-sm shadow border-0 me-2">EDIT</NuxtLink>
                                        <button @click="deleteTodo(todo.id)" class="btn btn-sm btn-danger rounded-sm shadow border-0">DELETE</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style>
/* Tambahkan CSS tambahan jika diperlukan */
</style>