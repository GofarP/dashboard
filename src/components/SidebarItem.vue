<template>
    <div>
        <div @click="handleClick"
            class="flex items-center justify-between cursor-pointer hover:bg-cyan-800 p-2 rounded transition">
            <div class="flex items-center space-x-2">
                <i :class="`pi pi-${icon}`"></i>
                <router-link v-if="!hasChildren" :to="to" class="text-white">{{ label }}</router-link>
                <span v-else class="text-white">{{ label }}</span>
            </div>
            <i v-if="hasChildren" class="pi" :class="isOpen ? 'pi-chevron-down' : 'pi-chevron-right'"></i>
        </div>

        <transition name="fade">
            <div v-if="hasChildren && isOpen" class="ml-6 mt-1 space-y-1">
                <router-link v-for="(item, index) in children" :key="index" :to="item.to"
                    :class="['block text-sm text-white hover:bg-cyan-800 p-3', item.class]">
                    {{ item.label }}
                </router-link>

            </div>
        </transition>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
    icon: String,
    label: String,
    to: String,
    children: {
        type: Array,
        default: () => []
    }
})

const isOpen = ref(false)
const hasChildren = computed(() => props.children.length > 0)

const handleClick = () => {
    if (hasChildren.value) {
        isOpen.value = !isOpen.value
    }
}
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}

.fade-enter-to,
.fade-leave-from {
    opacity: 1;
}
</style>