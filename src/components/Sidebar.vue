<template>
    <div class="relative flex min-h-screen">
        <!-- Sidebar -->
        <div class="bg-cyan-600 text-cyan-100 w-64 space-y-6 px-2 py-4 fixed inset-y-0 left-0 transform transition-transform duration-200 ease-in-out z-50"
            :class="showSidebar ? 'translate-x-0' : '-translate-x-full'">

            <a href="" class="flex items-center space-x-2 px-4">
                <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                    stroke="currentColor" class="h-10 w-10">
                    <path stroke-linecap="round" stroke-linejoin="round"
                        d="m21 7.5-2.25-1.313M21 7.5v2.25m0-2.25-2.25 1.313M3 7.5l2.25-1.313M3 7.5l2.25 1.313M3 7.5v2.25m9 3 2.25-1.313M12 12.75l-2.25-1.313M12 12.75V15m0 6.75 2.25-1.313M12 21.75V19.5m0 2.25-2.25-1.313m0-16.875L12 2.25l2.25 1.313M21 14.25v2.25l-2.25 1.313m-13.5 0L3 16.5v-2.25" />
                </svg>
                <span class="text-2xl font-extrabold">Dashboard</span>
            </a>

            <nav>
                <a href="/"
                    class="flex items-center py-3 space-x-2 px-4 hover:bg-cyan-700 rounded group transition duration-200">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="size-6 stroke-white group-hover:stroke-cyan-300">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="m2.25 12 8.954-8.955c.44-.439 1.152-.439 1.591 0L21.75 12M4.5 9.75v10.125c0 .621.504 1.125 1.125 1.125H9.75v-4.875c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125V21h4.125c.621 0 1.125-.504 1.125-1.125V9.75M8.25 21h8.25" />
                    </svg>
                    <span class="text-white group-hover:text-cyan-300">Dashboard</span>
                </a>

                <a href="/create"
                    class="flex items-center py-3 space-x-2 px-4 hover:bg-cyan-700 rounded group transition duration-200">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="size-6 stroke-white group-hover:stroke-cyan-300">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M15 19.128a9.38 9.38 0 0 0 2.625.372 9.337 9.337 0 0 0 4.121-.952 4.125 4.125 0 0 0-7.533-2.493M15 19.128v-.003c0-1.113-.285-2.16-.786-3.07M15 19.128v.106A12.318 12.318 0 0 1 8.624 21c-2.331 0-4.512-.645-6.374-1.766l-.001-.109a6.375 6.375 0 0 1 11.964-3.07M12 6.375a3.375 3.375 0 1 1-6.75 0 3.375 3.375 0 0 1 6.75 0Zm8.25 2.25a2.625 2.625 0 1 1-5.25 0 2.625 2.625 0 0 1 5.25 0Z" />
                    </svg>
                    <span class="text-white group-hover:text-cyan-300">Karyawan</span>
                </a>

                <a href="/"
                    class="flex items-center py-3 space-x-2 px-4 hover:bg-cyan-700 rounded group transition duration-200">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="size-6 stroke-white group-hover:stroke-cyan-300">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M19.5 14.25v-2.625a3.375 3.375 0 0 0-3.375-3.375h-1.5A1.125 1.125 0 0 1 13.5 7.125v-1.5a3.375 3.375 0 0 0-3.375-3.375H8.25m2.25 0H5.625c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125V11.25a9 9 0 0 0-9-9Z" />
                    </svg>
                    <span class="text-white group-hover:text-cyan-300">Projects</span>
                </a>


                <a href="/"
                    class="flex items-center py-3 space-x-2 px-4 hover:bg-cyan-700 rounded group transition duration-200">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="size-6 stroke-white group-hover:stroke-cyan-300">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M6.75 3v2.25M17.25 3v2.25M3 18.75V7.5a2.25 2.25 0 0 1 2.25-2.25h13.5A2.25 2.25 0 0 1 21 7.5v11.25m-18 0A2.25 2.25 0 0 0 5.25 21h13.5A2.25 2.25 0 0 0 21 18.75m-18 0v-7.5A2.25 2.25 0 0 1 5.25 9h13.5A2.25 2.25 0 0 1 21 11.25v7.5m-9-6h.008v.008H12v-.008ZM12 15h.008v.008H12V15Zm0 2.25h.008v.008H12v-.008ZM9.75 15h.008v.008H9.75V15Zm0 2.25h.008v.008H9.75v-.008ZM7.5 15h.008v.008H7.5V15Zm0 2.25h.008v.008H7.5v-.008Zm6.75-4.5h.008v.008h-.008v-.008Zm0 2.25h.008v.008h-.008V15Zm0 2.25h.008v.008h-.008v-.008Zm2.25-4.5h.008v.008H16.5v-.008Zm0 2.25h.008v.008H16.5V15Z" />
                    </svg>

                    <span class="text-white group-hover:text-cyan-300">Calendar</span>
                </a>

                <a href="/logout"
                    class="flex items-center py-3 space-x-2 px-4 hover:bg-cyan-700 rounded group transition duration-200">
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="h-6 w-6 stroke-white group-hover:stroke-cyan-300">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M5.636 5.636a9 9 0 1 0 12.728 0M12 3v9" />
                    </svg>
                    <span class="text-white group-hover:text-cyan-300">Logout</span>
                </a>

            </nav>
        </div>

        <!-- Main content -->
        <div :class="showSidebar ? 'ml-64' : 'ml-0'" class="flex-1 transition-all duration-200">

            <!-- Header -->
            <div class="relative bg-white shadow px-2 py-4 flex items-center justify-between">
                <button @click="showSidebar = !showSidebar"
                    class="text-cyan-600 font-extrabold z-50 relative cursor-pointer">
                    <!-- Icon -->
                    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5"
                        stroke="currentColor" class="h-8 w-8">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M3.75 5.25h16.5m-16.5 4.5h16.5m-16.5 4.5h16.5m-16.5 4.5h16.5" />
                    </svg>
                </button>

                <div class="flex items-center ml-auto">
                    <!-- Gambar Profil -->
                    <img src="data:image/jpeg;base64,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"
                        alt="Profil" class="rounded-full h-10 w-10 mr-3">

                    <!-- Nama -->
                    <p class="mr-7 text-cyan-600"
                        :class="{ 'hidden md:block': showSidebar, 'text-cyan-600': !showSidebar }">Gofar Putra Perdana
                    </p>

                </div>

            </div>

            <main class="flex-1 overflow-x-hidden overflow-y-auto bg-gray-100 p-4">
                <div class="container mx-auto">
                    <slot />
                </div>
            </main>


        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';

const showSidebar = ref(true);
        
</script>


<style scoped></style>
