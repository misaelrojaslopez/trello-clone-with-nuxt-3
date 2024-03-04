<script setup>
  const route = useRoute()

  console.log(route.name)

  /* in home page will just "index" but in a task will be index-tasks-id */
</script>
<template>
  <Html class="bg-slate-600">
    <div>
    <NuxtPage />
  </div>  
  </Html>
</template>
