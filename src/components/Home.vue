<template>
    <Sidebar>
        <h1 class="p-8 text-2xl font-extrabold text-cyan-600 mt-2">Data Karyawan</h1>
        <div class="bg-white rounded-lg shadow-lg p-6 ml-5 mr-5">
            <div class="overflow-x-auto p-4">
                <div v-if="loading">Loading...</div>
                <div v-else-if="error" class="text-red-500">{{ error }}</div>
                <div v-else>
                    <div class="flex justify-end mb-3">
                        <input type="text" v-model="searchQuery" @input="debounceSearch"
                            class="border border-cyan-600  focus:outline-none focus:ring-2 focus:ring-cyan-500"
                            placeholder="search...">
                    </div>

                    <div class="flex justify-end mb-3">
                        <button @click="prepareAdd"
                            class="bg-green-400 hover:bg-green-500 text-white py-1 px-3 rounded">Tambah
                            Karyawan</button>
                    </div>

                    <table class="min-w-full border-gray-200 rounded-lg shadow">
                        <thead class="bg-cyan-600 text-white">
                            <tr>
                                <th class="py-3 px-6 text-left">#</th>
                                <th class="py-3 px-6 text-left">Nama</th>
                                <th class="py-3 px-6 text-left">Email</th>
                                <th class="py-3 px-6 text-left">Aksi</th>
                            </tr>
                        </thead>
                        <tbody class="text-gray-700">
                            <tr v-for="(datas, index) in users" :key="datas.id" class="border-t">
                                <td class="py-3 px-6">{{ index + 1 + (currentPage - 1) * perPage }}</td>
                                <td class="py-3 px-6">{{ datas.name }}</td>
                                <td class="py-3 px-6">{{ datas.email }}</td>
                                <td class="py-3 px-6 flex items-center gap-2">
                                    <button @click="editData(datas)"
                                        class="bg-yellow-400 hover:bg-yellow-500 text-white py-1 px-3 rounded">Edit</button>
                                    <button @click="deleteData(datas.id)"
                                        class="bg-red-500 hover:bg-red-600 text-white py-1 px-3 rounded">Hapus</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>

                    <div class="flex justify-end items-center mt-7">
                        <button @click="goToPage(firstPageUrl)" :disabled="!prevPageUrl"
                            class="px-3 py-1 bg-cyan-600 text-white rounded disabled:opacity-50 mr-3">First</button>
                        <button @click="goToPage(prevPageUrl)" :disabled="!prevPageUrl"
                            class="px-3 py-1 bg-cyan-600 text-white rounded disabled:opacity-50">Prev</button>
                        <button v-for="page in paginationLinks" :key="page.label" @click="goToPage(page.url)" :class="{
                            'bg-cyan-600 text-white': page.active,
                            'bg-white text-cyan-600 hover:bg-cyan-100': !page.active
                        }" class="px-3 py-1 mx-1 rounded">{{ page.label }}</button>
                        <button @click="goToPage(nextPageUrl)" :disabled="!nextPageUrl"
                            class="px-3 py-1 bg-cyan-600 text-white rounded disabled:opacity-50">Next</button>
                        <button @click="goToPage(lastPageUrl)" :disabled="!nextPageUrl"
                            class="px-3 py-1 bg-cyan-600 text-white rounded disabled:opacity-50 ml-3">Last</button>
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

const searchQuery = ref('');
const users = ref([]);
const loading = ref(true);
const error = ref(null);

const currentPage = ref(1);
const lastPage = ref(1);
const perPage = ref(5);
const paginationLinks = ref([]);
const prevPageUrl = ref(null);
const nextPageUrl = ref(null);
const firstPageUrl = ref(null);
const lastPageUrl = ref(null);

let name = ref('');
let email = ref('');
let password = ref('');

const fetchUsers = async () => {
    loading.value = true;
    error.value = null;

    try {
        const response = await axios.get(`https://gofarputraperdana.my.id/api/user`, {
            params: { page: currentPage.value, limit: perPage.value }
        });

        const result = response.data;
        users.value = result.data;
        lastPage.value = result.last_page;

        const filteredLinks = result.links.filter(link =>
            link.url !== null && link.label !== '&laquo; Previous' && link.label !== 'Next &raquo;'
        );

        paginationLinks.value = limitPaginationLinks(filteredLinks, currentPage.value, lastPage.value);
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

const searchUsers = async () => {
    loading.value = true;
    error.value = null;

    try {
        const response = await axios.get('https://gofarputraperdana.my.id/api/usersearch', {
            params: {
                page: currentPage.value,
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

        paginationLinks.value = limitPaginationLinks(filteredLinks, currentPage.value, lastPage.value);
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
        if (isEdit.value==true && selectedId.value != '') {
            let response=await  axios.put(`https://gofarputraperdana.my.id/api/users/${selectedId.value}`, {
                name: name.value,
                email: email.value,
                password:password.value
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

function limitPaginationLinks(links, currentPage, lastPage) {
    if (lastPage <= 5) return links;

    let start, end;
    if (currentPage <= 3) {
        start = 1;
        end = 5;
    } else if (currentPage >= lastPage - 2) {
        start = lastPage - 4;
        end = lastPage;
    } else {
        start = currentPage - 2;
        end = currentPage + 2;
    }

    return links.filter(link => {
        const pageNum = parseInt(link.label);
        return !isNaN(pageNum) && pageNum >= start && pageNum <= end;
    });
}

function goToPage(url) {
    if (!url) return;
    const params = new URLSearchParams(url.split('?')[1]);
    const page = params.get('page');
    if (page) {
        currentPage.value = parseInt(page);
    }
}

onMounted(fetchUsers);
watch(currentPage, fetchUsers);
</script>
