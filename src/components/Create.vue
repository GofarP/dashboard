<template>
    <Sidebar>
        <h1 class="p-8 text-2xl font-extrabold text-cyan-600 mt-2">
            Tambah Karyawan
        </h1>
        <div class="bg-white rounded-lg shadow-lg p-6 ml-5 mr-5">
            <label for="nama" class="block text-cyan-600  mb-2">Nama</label>
            <input type="text" id="nama"
                class="w-full mb-3 px-4 py-2 border border-cyan-600 rounded-lg focus:outline-none focus:ring-2 focus:ring-cyan-500"
                placeholder="Masukkan nama" />
            <label for="email" class="block text-cyan-600  mb-2">Email</label>
            <input type="email" id="email"
                class="w-full mb-3 px-4 py-2 border border-cyan-600 rounded-lg focus:outline-none focus:ring-2 focus:ring-cyan-500"
                placeholder="Masukkan Email" />

            <button @click="handleClick" class="bg-cyan-600 rounded-lg text-white p-2 w-full mt-5">
                Simpan
            </button>
        </div>
    </Sidebar>
</template>

<script setup>
import Sidebar from './Sidebar.vue';

const handleClick = () => {
  alert('Data disimpan!');
};
</script>

<style scoped></style>