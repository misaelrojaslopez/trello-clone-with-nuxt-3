<script setup>
import { useBoardStore } from '../stores/boardStore'

const props = defineProps({
    column: {
        type: Object,
        require: true
    },
    columnIndex: {
        type: Number,
        require: true
    }
})

const boardStore = useBoardStore()
const router = useRouter()

const editNameState= ref(false)
const newTaskName= ref('')

/**
* Column funtions
*/

function deleteColumn(columnIndex) {
   boardStore.deleteColumn(columnIndex)
}

/**
* Tasks funtions
*/

function goToTask(taskId) {
   router.push(`/tasks/${taskId}`)
}

function addTask(taskId) {
   boardStore.addTask({
    taskName: newTaskName.value,
    columnIndex: props.columnIndex
   })
   newTaskName.value = ''
}

/**
 * Drag and drop
 */

function pickupTask(event, { fromColumnIndex, taskIndex}) {
    console.log(event)
    event.dataTransfer.affectAllowed = 'move'
    event.dataTransfer.dropEffect = 'move'
    event.dataTransfer.setData('Type', 'task')
    event.dataTransfer.setData('from-column-index', fromColumnIndex)
    event.dataTransfer.setData('task-index', taskIndex)
}

function pickupColumn(event, fromColumnIndex) {
    console.log(event)
    event.dataTransfer.affectAllowed = 'move'
    event.dataTransfer.dropEffect = 'move'
    event.dataTransfer.setData('Type', 'column')
    event.dataTransfer.setData('from-column-index', fromColumnIndex)
}

function dropItem(event, toColumnIndex) {
    console.log(event)
    const type = event.dataTransfer.getData('type')
    const fromColumnIndex = event.dataTransfer.getData('from-column-index')        

    if( type === 'task') {
        const taskIndex = event.dataTransfer.getData('task-index')

        boardStore.moveTask({
            taskIndex,
            fromColumnIndex,
            toColumnIndex
        })
    } else if (type === 'column') {
        const fromColumnIndex = event.dataTransfer.getData('from-column-index')

        boardStore.moveColumn({
            fromColumnIndex,
            toColumnIndex
        })
    }
}
</script>

<template>
<UContainer
    class="column"
    draggable="true"
    @dragstart.self="pickupColumn($event, columnIndex)"
    @dragenter.prevent
    @dragover.prevent
    @drop.stop="dropItem($event, columnIndex)"
>
    <div class="column-header mb-4">
    <div class="flex items-center">
        <UInput 
            v-if="editNameState"
            type="text"
            v-model="column.name" 
        />
        <h2 v-else>{{ column.name }}</h2>
    </div>
    <div>
        <UButton
            icon="i-heroicons-pencil-square"
            class="mr-2"
            @click="editNameState = !editNameState"
        />
        <UButton
            icon="i-heroicons-trash"
            color="red"
            @click="deleteColumn(columnIndex)"
        />
    </div>
    </div>
    
    <ul>
    <li v-for="(task, taskIndex) in column.tasks" :key="task.id">
        <UCard class="mb-4"
            draggable="true"
            @click="goToTask(task.id)"
            @dragstart="pickupTask($event, {
                fromColumnIndex: columnIndex,
                taskIndex,
            })
        ">
            <b> {{  task.name }}</b>
            <p> {{ task.description }}</p>
        </UCard> 
    </li>
    </ul>
    <UInput
        v-model="newTaskName"
        type="text"
        placeholder="Create new task"
        icon="i-heroicons-plus-circle-solid"
        @keyup.enter="addTask"
    />
</UContainer>
</template>