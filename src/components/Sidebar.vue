<template>
    <div class="relative min-h-screen">
        <!-- Sidebar -->
        <transition name="slide">
            <div v-if="showSidebar" class="fixed top-0 left-0 h-full w-64 bg-cyan-700 text-white z-50 p-4 shadow-lg">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-xl font-bold">Dashboard</h2>
                    <button @click="toggleSidebar" class="text-white text-2xl leading-none">
                        &times;
                    </button>
                </div>
                <nav class="space-y-4">
                    <SidebarItem icon="home" label="Dashboard" to="/" />
                    <SidebarItem icon="user-plus" label="Karyawan" to="/create" />
                    <SidebarItem icon="folder" label="Projects" to="/projects" />
                    <SidebarItem icon="calendar" label="Calendar" to="/calendar" />
                    <SidebarItem icon="power-off" label="Logout" to="/logout" />
                    <SidebarItem icon="user-plus" label="Karyawan" :children="[
                        { label: 'List Karyawan', to: '/karyawan', class: 'text-blue-200 hover:text-white' },
                        { label: 'Tambah Karyawan', to: '/karyawan/create', class: 'text-green-200 hover:text-white' }
                    ]" />
                </nav>
            </div>
        </transition>

        <!-- Header -->
        <header class="bg-white shadow px-4 py-4 flex items-center">
            <button @click="toggleSidebar" class="text-cyan-700">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M4 6h16M4 12h16M4 18h16"></path>
                </svg>
            </button>
            <h1 class="ml-4 text-xl font-semibold text-cyan-800">{{ props.pageTitle }}</h1>

            <div class="flex items-center ml-auto">
                <img src="data:image/jpeg;base64,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"
                    alt="Profil" class="rounded-full h-10 w-10 mr-3">
                <p class="text-cyan-800 text-md mt-1">Gofar Putra Perdana</p>
            </div>

        </header>

        <!-- Page Content -->
        <main class="p-6">
            <slot />
        </main>
    </div>
</template>

<script setup>
import { ref, defineProps } from 'vue'
import SidebarItem from './SidebarItem.vue'

const props = defineProps({
    pageTitle: String
});

const showSidebar = ref(false)
const toggleSidebar = () => {
    showSidebar.value = !showSidebar.value
}
</script>

<style scoped>
.slide-enter-active,
.slide-leave-active {
    transition: transform 0.3s ease;
}

.slide-enter-from {
    transform: translateX(-100%);
}

.slide-enter-to {
    transform: translateX(0);
}

.slide-leave-from {
    transform: translateX(0);
}

.slide-leave-to {
    transform: translateX(-100%);
}
</style>
