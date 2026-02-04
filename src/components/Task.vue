<script setup>
    const { task } = defineProps({
        task: {
            type: Object,
            required: true
        }
    })

    import { ref, computed, onMounted } from 'vue'
    import FileIcon from './icons/FileIcon.vue';
    import LinkIcon from './icons/LinkIcon.vue';

    const modules = ref({})

    onMounted(async () => {
        try {
            const res = await fetch('/modules.json')
            const data = await res.json()
            modules.value = data.modules || {}
        } catch (err) {
            console.error('Failed to load modules.json', err)
            modules.value = {}
        }
    })

    const mod = computed(() => {
        const key = task?.module
        if (!key) return null

        return modules.value[key] || null
    })

    function formatDateString(d) {
        if (!d || d === 'unknown') return 'Date inconnue'

        const dt = new Date(d)
        if (isNaN(dt)) return d

        return dt.toLocaleDateString('fr-FR', { weekday: 'long', day: '2-digit', month: 'short', year: 'numeric' })
    }
</script>
<template>
    <div :data-id="task.id" class="cursor-pointer bg-white border border-zinc-200 rounded-3xl p-6 dark:bg-zinc-900 dark:border-zinc-800">
        <div class="flex">
            <div class="shrink-0 flex items-center w-10">
                <span class="text-2xl">{{ task.icon }}</span>
            </div>
            <div class="-space-y-2">
                <h3 class="text-lg font-bold">{{ task.title }}</h3>
                <span class="text-zinc-400 text-xs font-semibold">Pour le {{ formatDateString(task.date) }}</span>
            </div>
        </div>
        <div class="mt-2">
            <p class="font-semibold">{{ task.description }}</p>
        </div>
        <div class="grid grid-cols-1 gap-2 mt-4" v-if="task.attachments && task.attachments.length > 0">
            <div v-for="file in task.attachments" :key="file.name" class="flex items-center gap-1.5 bg-zinc-500/5 rounded-2xl px-4 py-4">
                <FileIcon v-if="file.download" color="#ffffffa0" class="inline w-8 h-8" />
                <LinkIcon v-else color="#ffffffa0" class="inline w-8 h-8" />
                <div class="-space-y-2">
                    <a :href="file.url" :download="file.download" class="text-sm font-semibold line-clamp-1 hover:underline">
                        {{ file.title || file.name }}
                    </a>
                    <span class="text-xs text-zinc-400/50">{{ file.name }}</span>
                </div>
            </div>
        </div>
        <div class="flex items-center mt-4">
            <span class="shrink-0 text-indigo-500 text-sm font-bold">{{ task.module || '' }} - {{ mod ? mod.title : '' }}</span>
            <span class="grow"></span>
            <span class="shrink-0 bg-emerald-300/10 text-emerald-300 text-sm font-bold border border-emerald-300/50 rounded-full px-3 py-1 min-w-8">{{ task.format }}</span>
        </div>
    </div>
</template>