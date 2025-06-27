<script setup lang="ts">
        const board = [[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12], [13, 14, 15, 16]];
</script>
<template>
        <table>
                <tr v-for="row in board">
                        <td v-for="cell in row">
                                {{cell}}
                        </td>
                </tr>
                </table>
</template>
