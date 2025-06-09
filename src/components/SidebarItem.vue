<template>
    <a :href="to" class="flex items-center space-x-3 px-3 py-2 rounded hover:bg-cyan-600 transition group">
        <component :is="iconComponent" class="w-6 h-6 text-white group-hover:text-cyan-200" />
        <span class="group-hover:text-cyan-200">{{ label }}</span>
    </a>
</template>

<script setup>
import { computed } from 'vue'
import {
    HomeIcon,
    UserPlusIcon,
    FolderIcon,
    CalendarIcon,
    ArrowLeftOnRectangleIcon,
} from '@heroicons/vue/24/outline'

const props = defineProps({
    icon: String,
    label: String,
    to: String,
})

const iconMap = {
    home: HomeIcon,
    'user-plus': UserPlusIcon,
    folder: FolderIcon,
    calendar: CalendarIcon,
    logout: ArrowLeftOnRectangleIcon,
}

const iconComponent = computed(() => iconMap[props.icon] || HomeIcon)
</script>
