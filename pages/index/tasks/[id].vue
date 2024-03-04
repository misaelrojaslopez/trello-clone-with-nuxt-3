<script setup>
import { useBoardStore } from '~/stores/boardStore'

const boardStore = useBoardStore()
const route = useRoute()
const router = useRouter()

const taskId = route.params.id

const task = computed(()=> {
    return boardStore.getTask(taskId)
})

function deleteTask() {
    boardStore.deleteTask(taskId)
    router.push('/')
}
</script>
<template>
    <div class="task-wrapper">
        <div class="task-view">
            <section v-if="task" class="w-full">
                <UFormGroup label="Name" class="w-full mb-4">
                    <UInput type="text" v-model="task.name"/>
                </UFormGroup>
                <UFormGroup label="Description" class="w-full mb-4">
                    <UTextarea v-model="task.description" />
                </UFormGroup>
                <div class="flex justify-end w-full">
                    <UButton
                        icon="i-heroicons-trash"
                        color="red"
                        @click="deleteTask"
                    >Delete task</UButton>
                </div>
            </section>
            <section v-else>
                <p class="mb-4">Task not found </p>   
            </section>
        </div>
    </div>
</template>
