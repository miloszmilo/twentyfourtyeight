<script setup lang="ts">
defineProps<{ board: Array<Array<Number>> }>()
</script>
<template>
        <table class="flex flex-col items-center">
                <tr v-for="row in board" class="flex flex-row justify-between">
                        <td v-for="cell in row" class="p-2">
                                {{cell}}
                        </td>
                </tr>
                </table>
</template>
