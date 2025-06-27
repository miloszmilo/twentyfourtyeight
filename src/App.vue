<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import Board from './components/organisms/Board.vue'
import {reactive, ref} from 'vue'
const board = reactive([[2, 2, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0], [2, 0, 0, 0]]);
const score = ref(0);

function moveDown() {
        for (let i = 0; i < board.length - 1; i++) {
                for (let j = 0; j < board[i].length; j++) {
                        if (board[i][j] === 0) continue;
                        if (board[i+1][j] === 0) {
                                board[i+1][j] = board[i][j];
                                board[i][j] = 0;
                                continue;
                        }
                        if (board[i][j] === board[i+1][j]) {
                                board[i+1][j] += board[i][j];
                                board[i][j] = 0;
                                score.value += board[i+1][j];
                                continue;
                        }
                }
        }
}
</script>

<template>
        <main>
                <div>
                        <span>HIGHSCORE {{score}}</span>
                        <Board :board="board" @click="moveDown"/>
                </div>
        </main>
</template>
