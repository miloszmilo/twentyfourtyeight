<script setup lang="ts">
defineProps<{ board: Array<Array<Number>> }>()
</script>
<template>
        <table class="flex flex-col items-stretch bg-neutral-400 rounded-lg m-8">
                <tr v-for="row in board" class="flex flex-row justify-between" >
                        <td v-for="cell in row" class="p-2 bg-neutral-500 rounded-lg min-w-[6ch] text-center text-white m-2">
                                {{cell}}
                        </td>
                </tr>
                </table>
</template>
