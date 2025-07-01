<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import Board from './components/organisms/Board.vue'
import {reactive, ref} from 'vue'
const board = reactive([[2, 2, 2, 2], [0, 2, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]);
const isGameOver = ref(false);
const score = ref(0);

const vectors = new Map<Direction, Array<Number>>([
        ["UP", [0, -1]],
        ["DOWN", [0, 1]],
        ["LEFT", [-1, 0]],
        ["RIGHT", [1, 0]],
]);

type Direction = "UP" | "DOWN" | "LEFT" | "RIGHT";

const sleep = (delay) => new Promise((resolve) => setTimeout(resolve, delay))

function move(direction: Direction) : None {
        const vectors = new Map<Direction, Array<Number>>([
                ["UP", moveUp],
                ["DOWN", moveDown],
                ["LEFT", moveLeft],
                ["RIGHT", moveRight],
        ]);
        const moveFunction = vectors.get(direction);
        moveFunction()
        // Temporary Disable
        // spawnNewTiles();
}

function moveUp() {
        for (let i = board.length-1; i > 0; i--) {
                for (let j = 0; j < board[i].length; j++) {
                        if (board[i][j] === 0) continue;
                        if (board[i-1][j] === 0) {
                                board[i-1][j] = board[i][j];
                                board[i][j] = 0;
                                continue;
                        }
                        if (board[i][j] === board[i-1][j]) {
                                board[i-1][j] += board[i][j];
                                board[i][j] = 0;
                                score.value += board[i-1][j];
                                continue;
                        }
                }
        }
}

function moveDown() {
        for (let i = board.length-1; i > 0; i--) {
                for (let j = 0; j < board[i].length; j++) {
                        if (board[i][j] === 0 && board.slice(0, i).map((a) => {return a[j]}).some(e => e > 0)) {
                        const indexToMove = board.slice(0, i).map((a) => {return a[j]}).findIndex(e => e > 0);
                                board[i][j] = board[indexToMove][j];
                                board[indexToMove][j] = 0;
                                continue;
                        }
                        if (board[i][j] === board[i-1][j]) {
                                board[i-1][j] += board[i][j];
                                board[i][j] = 0;
                                score.value += board[i-1][j];
                                // Move any other tile
                                for (let k = i; k > 0; k--) {
                                        if (board[k-1][j] === 0) continue;
                                        if (board[k][j] === 0) {
                                                board[k][j] = board[k-1][j];
                                                board[k-1][j] = 0;
                                                continue;
                                        }
                                }
                                continue;
                                continue;
                        }
                }
        }
}

function moveLeft() {
        for (let i = 0; i < board.length; i++) {
                for (let j = 0; j < board[i].length; j++) {
                        if (board[i][j] === 0 && board[i].slice(j, board[i].length).some(e => e > 0)) {
                        const indexToMove = board[i].slice(j, board[i].length).findIndex(e => e > 0);
                                board[i][j] = board[i][indexToMove];
                                board[i][indexToMove] = 0;
                                continue;
                        }
                        if (board[i][j] === board[i][j+1]) {
                                board[i][j] += board[i][j+1];
                                board[i][j+1] = 0;
                                score.value += board[i][j];
                                // Move any other tile
                                for (let k = j; k < board[i].length - 1; k++) {
                                        if (board[i][k+1] === 0) continue;
                                        if (board[i][k] === 0) {
                                                board[i][k] = board[i][k+1];
                                                board[i][k+1] = 0;
                                                continue;
                                        }
                                }
                                continue;
                        }
                }
        }
}

function moveRight() {
        for (let i = 0; i < board.length; i++) {
                for (let j = board[i].length; j > 0; j--) {
                        if (board[i][j] === 0 && board[i].slice(0, j).some(e => e > 0)) {
                        const indexToMove = board[i].slice(0, j).findIndex(e => e > 0);
                                board[i][j] = board[i][indexToMove];
                                board[i][indexToMove] = 0;
                                continue;
                        }
                        if (board[i][j] !== board[i][j-1]) continue;
                        board[i][j] += board[i][j-1];
                        board[i][j-1] = 0;
                        score.value += board[i][j];
                        // Move any other tile
                        for (let k = j; k > 0; k--) {
                                if (board[i][k-1] === 0) continue;
                                if (board[i][k] === 0) {
                                        board[i][k] = board[i][k-1];
                                        board[i][k-1] = 0;
                                        continue;
                                }
                        }
                        continue;
                }
        }
}

function spawnNewTiles() {
        // 90% for 2 value
        // 10% for 4 value
        const chance = Math.random();
        let tile = 2;
        let isTileInserted = false;
        if (chance > 0.9)
                tile = 4;
        let isLost = true;
        while (!isTileInserted) {
                for (let i = 0; i < board.length - 1; i++) {
                        for (let j = 0; j < board[i].length; j++) {
                                if (board[i][j] !== 0) continue;
                                isLost = false;
                                const shouldChangeTile = Math.round(Math.random());
                                if (shouldChangeTile === 1)
                                        board[i][j] = tile;
                                        isTileInserted = true;
                                        return;
                        }
                }
        }
        if (isLost)
                isGameOver.value = true;
}

function startGame() {
        isGameOver.value = false;
        const tile = 2;
        let tilesLeft = 2
        while (tilesLeft > 0) {
                for (let i = 0; i < board.length - 1; i++) {
                        for (let j = 0; j < board[i].length; j++) {
                                if (tilesLeft === 0) return;
                                const shouldChangeTile = Math.round(Math.random());
                                if (shouldChangeTile === 0)
                                        continue;
                                board[i][j] = tile;
                                tilesLeft--;
                        }
                }
        };
}


</script>

<template>
        <main>
                <div>
                        <span>HIGHSCORE {{score}}</span>
                        <span>GAME OVER {{isGameOver ? 'LOST' : 'PLAYING'}}</span>
                        <button @click="startGame">Start</button>
                        <Board :board="board"/>
                        <button @click="move('UP')">UP</button>
                        <button @click="move('DOWN')">DOWN</button>
                        <button @click="move('LEFT')">LEFT</button>
                        <button @click="move('RIGHT')">RIGHT</button>
                </div>
        </main>
</template>
