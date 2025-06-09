<template>
    <Sidebar :page-title="message">
        <div class="bg-white rounded-lg shadow-lg p-6 ml-5 mr-5">
            <div class="overflow-x-auto p-4">
                <div v-if="loading">Loading...</div>
                <div v-else-if="error" class="text-red-500">{{ error }}</div>
                <div v-else>
                    <div class="flex justify-end mb-3 w-full">
                        <input type="text" v-model="searchQuery" @input="debounceSearch"
                            class="w-full md:w-auto border border-cyan-600 focus:outline-none focus:ring-2 focus:ring-cyan-500"
                            placeholder="search...">
                    </div>

                    <div class="flex justify-end mb-3 w-full">
                        <button @click="prepareAdd"
                            class="w-full md:w-auto  bg-green-400 hover:bg-green-500 text-white py-1 px-3 rounded">Tambah
                            Karyawan</button>
                    </div>

                    <div class="overflow-x-auto">
                        <table class="min-w-full divide-y divide-gray-200 text-sm">
                            <thead class="bg-cyan-600 text-white text-center uppercase text-xs">
                                <tr>
                                    <th scope="col" class="px-6 py-3 font-medium">No</th>
                                    <th scope="col" class="px-6 py-3  font-medium">Nama</th>
                                    <th scope="col" class="px-6 py-3  font-medium">Email</th>
                                    <th scope="col" class="px-6 py-3  font-medium">Action</th>

                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-200">
                                <tr v-for="(user, index) in users" :key="user.id">
                                    <td class="px-6 py-4 whitespace-nowrap text-center">{{ index + 1 }}</td>
                                    <td class="px-6 py-4 whitespace-nowrap">{{ user.name }}</td>
                                    <td class="px-6 py-4 whitespace-nowrap">{{ user.email }}</td>
                                    <td class="flex">
                                        <button></button>
                                    </td>
                                </tr>
                            
                            </tbody>
                        </table>
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
    </Sidebar>
</template>

<script setup>
import Sidebar from './Sidebar.vue';
import { ref, onMounted, watch } from 'vue';
import debounce from 'lodash/debounce';
import axios from 'axios';

let showModal = ref(false);
let isEdit = ref(false);
let selectedId = ref(null);

const message = "Data Karyawan";

const searchQuery = ref('');
const users = ref([]);
const loading = ref(true);
const error = ref(null);

let perPage = 10;



let name = ref('');
let email = ref('');
let password = ref('');



const fetchUsers = async () => {
    loading.value = true;
    error.value = null;

    try {
        const response = await axios.get(`https://gofarputraperdana.my.id/api/user`, {
            params: { limit: perPage.value }
        });

        const result = response.data;
        users.value = result.data;


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
                limit: perPage.value,
                search: searchQuery.value
            }
        });

        const result = response.data;
        users.value = result.data;
        lastPage.value = result.last_page;

        const filteredLinks = result.links.filter(link =>
            link.url !== null && link.label !== '&laquo; Previous' && link.label !== 'Next &raquo;'
        );

        paginationLinks.value = limitPaginationLinks(filteredLinks, lastPage.value);
        lastPageUrl.value = result.last_page_url;
        firstPageUrl.value = result.first_page_url;
        prevPageUrl.value = result.prev_page_url;
        nextPageUrl.value = result.next_page_url;

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
        if (isEdit.value == true && selectedId.value != '') {
            let response = await axios.put(`https://gofarputraperdana.my.id/api/users/${selectedId.value}`, {
                name: name.value,
                email: email.value,
                password: password.value
            });

            console.log(response.status);

        } else {
            await axios.post('https://gofarputraperdana.my.id/api/users', {
                name: name.value,
                email: email.value,
                password: password.value
            });
        }

        // reset form
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
    console.log(data.id)

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



onMounted(fetchUsers);
watch(fetchUsers);
</script>
