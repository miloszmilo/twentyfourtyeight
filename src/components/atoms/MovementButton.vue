<script setup lang="ts">
        defineProps<{ onClick: () => void,  text: string}>()
</script>

<template>
        <button @click="onClick" class="cursor-pointer rounded-md hover:bg-blue-400 bg-blue-500 text-white p-2 w-fit">{{text}}</button>
</template>
