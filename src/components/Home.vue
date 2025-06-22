<template>
    <Sidebar :page-title="message">
        <div class="bg-white rounded-lg shadow-lg p-6 ml-5 mr-5">
            <div class="overflow-x-auto p-4">
                <div v-if="error" class="text-red-500">{{ error }}</div>
                <div v-else>
                    <div class="flex justify-end mb-3 w-full">
                        <input type="text" v-model="searchQuery" @input="debounceSearch"
                            class="w-full md:w-auto border border-cyan-600 focus:outline-none focus:ring-2 focus:ring-cyan-500"
                            placeholder="search...">
                    </div>

                    <div class="flex justify-end mb-3 w-full">
                        <button @click="prepareAdd"
                            class="w-full md:w-auto bg-green-400 hover:bg-green-500 text-white py-1 px-3 rounded">
                            Tambah Karyawan
                        </button>
                    </div>

                    <div class="overflow-x-auto">
                        <table class="min-w-full divide-y divide-gray-200 text-sm">
                            <thead class="bg-cyan-600 text-white text-center uppercase text-xs">
                                <tr>
                                    <th class="px-6 py-3 font-medium">No</th>
                                    <th class="px-6 py-3 font-medium">Nama</th>
                                    <th class="px-6 py-3 font-medium">Email</th>
                                    <th class="px-6 py-3 font-medium">Action</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-200">
                                <tr class="text-center" v-for="(user, index) in users.data" :key="user.id">
                                    <td class="px-6 py-4 whitespace-nowrap">{{ index + 1 }}</td>
                                    <td class="px-6 py-4 whitespace-nowrap">{{ user.name }}</td>
                                    <td class="px-6 py-4 whitespace-nowrap">{{ user.email }}</td>
                                    <td class="px-6 py-4 whitespace-nowrap flex justify-center gap-2">
                                        <button
                                            class="bg-yellow-400 hover:bg-yellow-500 text-white px-3 py-1 rounded transition"
                                            @click="editData(user)">Edit</button>
                                        <button
                                            class="bg-red-500 hover:bg-red-600 text-white px-3 py-1 rounded transition"
                                            @click="deleteData(user.id)">Hapus</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>

                        <div class="w-full flex justify-end">
                            <TailwindPagination class="text-end mt-3"
                                :data="users"
                                @pagination-change-page="fetchUsers"
                                :limit="1"
                                :keep-length="true"
                                :item-classes="['bg-white', 'text-gray-700', 'border', 'border-gray-300', 'px-2', 'py-1', 'text-sm']"
                                :active-classes="['bg-cyan-600', 'text-white']"
                                :prev-button-class="['bg-gray-200', 'text-gray-700', 'px-3', 'py-1', 'rounded', 'text-sm']"
                                :next-button-class="['bg-gray-200', 'text-gray-700', 'px-3', 'py-1', 'rounded', 'text-sm']"
                                prev-label="Prev" next-label="Next">
                                <template #prev-nav>&lt;</template>

                                <template #next-nav>&gt;</template>

                            </TailwindPagination>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div v-if="showModal" class="fixed inset-0 backdrop-blur-sm flex h-screen justify-center items-center z-50">
            <div class="bg-white p-6 rounded-lg w-full max-w-md shadow-lg">
                <h2 class="text-xl text-cyan-600 font-semibold mb-4">{{ isEdit ? 'Edit User' : 'Tambah User' }}</h2>
                <form @submit.prevent="simpanData">
                    <div class="mb-4">
                        <label class="block text-cyan-600 mb-1 text-sm font-medium">Nama</label>
                        <input type="text" v-model="name"
                            class="w-full px-3 py-2 border border-cyan-600 rounded focus:outline-none focus:ring-2 focus:ring-cyan-500">
                    </div>
                    <div class="mb-4">
                        <label class="block text-cyan-600 mb-1 text-sm font-medium">Email</label>
                        <input type="email" v-model="email"
                            class="w-full px-3 py-2 border border-cyan-600 rounded focus:outline-none focus:ring-2 focus:ring-cyan-500">
                    </div>
                    <div class="mb-4">
                        <label class="block text-cyan-600 mb-1 text-sm font-medium">Password</label>
                        <input type="password" v-model="password"
                            class="w-full px-3 py-2 border border-cyan-600 rounded focus:outline-none focus:ring-2 focus:ring-cyan-500">
                    </div>
                    <div class="flex justify-end gap-2">
                        <button type="button" @click="showModal = false"
                            class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600">Tutup</button>
                        <button type="submit"
                            class="px-4 py-2 bg-cyan-600 text-white rounded hover:bg-cyan-700">Simpan</button>
                    </div>
                </form>
            </div>
        </div>
        <div v-if="loading" class="fixed inset-0  flex h-screen justify-center items-center z-50">
            <div class="bg-white p-6 rounded-lg w-full max-w-md shadow-lg">
                <p class="text-2xl text-center">Loading</p>
            </div>
        </div>
    </Sidebar>
</template>

<script setup>

import Sidebar from './Sidebar.vue';
import { ref, onMounted } from 'vue';
import debounce from 'lodash/debounce';
import axios from 'axios';
import { TailwindPagination } from 'laravel-vue-pagination';

const message = "Data Karyawan";

const users = ref({
    data: [],
    current_page: 1,
    last_page: 1,
    per_page: 10,
    total: 0,
});
const loading = ref(true);
const error = ref(null);

const searchQuery = ref('');
const name = ref('');
const email = ref('');
const password = ref('');
const showModal = ref(false);
const isEdit = ref(false);
const selectedId = ref(null);

const fetchUsers = async (page = 1) => {
    loading.value = true;
    error.value = null;

    try {
        const response = await axios.get(`https://gofarputraperdana.my.id/api/user`, {
            params: {
                page: page,
                limit: users.value.per_page
            }
        });

        users.value = response.data;
        
    } catch (err) {
        error.value = err.response?.data?.message || err.message;
    } finally {
        loading.value = false;
    }
};

const searchUsers = async () => {
    loading.value = true;
    error.value = null;

    try {
        const response = await axios.get('https://gofarputraperdana.my.id/api/usersearch', {
            params: {
                limit: users.value.per_page,
                search: searchQuery.value
            }
        });

        users.value = response.data;
    } catch (err) {
        error.value = err.response?.data?.message || err.message;
    } finally {
        loading.value = false;
    }
};

const simpanData = async () => {
    if (!name.value || !email.value) {
        error.value = "Nama dan email wajib diisi.";
        return;
    }

    loading.value = true;
    error.value = null;

    try {
        if (isEdit.value && selectedId.value) {
            await axios.put(`https://gofarputraperdana.my.id/api/users/${selectedId.value}`, {
                name: name.value,
                email: email.value,
                password: password.value
            });
        } else {
            await axios.post('https://gofarputraperdana.my.id/api/users', {
                name: name.value,
                email: email.value,
                password: password.value
            });
        }

        // Reset form
        name.value = '';
        email.value = '';
        password.value = '';
        selectedId.value = null;
        isEdit.value = false;
        showModal.value = false;

        fetchUsers();
    } catch (err) {
        error.value = err.response?.data?.message || err.message;
    } finally {
        loading.value = false;
    }
};

const editData = (data) => {
    name.value = data.name;
    email.value = data.email;
    password.value = '';
    selectedId.value = data.id;
    isEdit.value = true;
    showModal.value = true;
};

const prepareAdd = () => {
    name.value = '';
    email.value = '';
    password.value = '';
    selectedId.value = null;
    isEdit.value = false;
    showModal.value = true;
};

const deleteData = async (id) => {
    if (!confirm('Yakin ingin menghapus data ini?')) return;

    loading.value = true;
    error.value = null;

    try {
        await axios.delete(`https://gofarputraperdana.my.id/api/users/${id}`);
        fetchUsers();
    } catch (err) {
        error.value = err.response?.data?.message || err.message;
    } finally {
        loading.value = false;
    }
};

const debounceSearch = debounce(searchUsers, 500);

onMounted(() => {
    fetchUsers();
});
</script>
