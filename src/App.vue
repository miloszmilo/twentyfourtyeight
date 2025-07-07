<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import Board from './components/organisms/Board.vue'
import { reactive, ref } from 'vue'
const board = reactive([[2, 2, 2, 0], [2, 2, 2, 0], [2, 2, 2, 0], [0, 0, 0, 0]]);
const isGameOver = ref(false);
const isWon = ref(false);
const score = ref(0);
const lifetimeHighscore = ref(localStorage.getItem("highscore") ?? 0);
const WINNING_TILE = 2048;
const MAX_TILE = 131072;

const vectors = new Map<Direction, Array<Number>>([
        ["UP", [0, -1]],
        ["DOWN", [0, 1]],
        ["LEFT", [-1, 0]],
        ["RIGHT", [1, 0]],
]);

type Direction = "UP" | "DOWN" | "LEFT" | "RIGHT";

const sleep = (delay) => new Promise((resolve) => setTimeout(resolve, delay))

function move(direction: Direction): None {
        const vectors = new Map<Direction, Array<Number>>([
                ["UP", moveUp],
                ["DOWN", moveDown],
                ["LEFT", moveLeft],
                ["RIGHT", moveRight],
        ]);
        const moveFunction = vectors.get(direction);
        moveFunction()
        spawnNewTiles();
}

function moveUp() {
        for (let i = board[0].length - 1; i >= 0; i--) {
                const col = board
                        .map(a => a[i])
                        .filter((e) => e !== 0);
                const mergedCol = [];
                for (let j = 0; j < col.length; j++) {
                        if (j === col.length-1) {
                                mergedCol.push(col[j]);
                                continue;
                        }
                        if (col[j] === col[j+1]) {
                                mergedCol.push(col[j]+col[j+1]);
                                score.value += col[j]+col[j+1];
                                saveHighscore();
                                j++;
                                continue;
                        }
                        mergedCol.push(col[j]);
                }
                const zeros = new Array(board[i].length - mergedCol.length).fill(0);
                const mergedColumn = mergedCol.concat([...zeros]);
                for (let k = 0; k < board.length; k++) {
                       board[k][i] = mergedColumn[k];
                }
        }
}

function moveDown() {
        for (let i = 0; i < board[0].length; i++) {
                const col = board
                        .map(a => a[i])
                        .filter((e) => e !== 0);
                const mergedCol = [];
                for (let j = 0; j < col.length; j++) {
                        if (j === col.length - 1) {
                                mergedCol.push(col[j]);
                                continue;
                        }
                        if (col[j] === col[j+1]) {
                                mergedCol.push(col[j]+col[j+1]);
                                score.value += col[j]+col[j+1];
                                saveHighscore();
                                j++;
                                continue;
                        }
                        mergedCol.push(col[j]);
                }
                const zeros = new Array(board[i].length - mergedCol.length).fill(0);
                const mergedColumn = zeros.concat([...mergedCol]);
                for (let k = board.length - 1; k >= 0; k--) {
                       board[k][i] = mergedColumn[k];
                }
        }
}

function moveLeft() {
        for (let i = 0; i < board.length; i++) {
                const row = board[i].filter((e) => e !== 0);
                const mergedRow = [];
                for (let j = row.length-1; j >= 0; j--) {
                        if (j === 0) {
                                mergedRow.unshift(row[j]);
                                continue;
                        }
                        if (row[j] === row[j-1]) {
                                mergedRow.unshift(row[j]+row[j-1]);
                                score.value += row[j]+row[j-1];
                                saveHighscore();
                                j--;
                                continue;
                        }
                        mergedRow.unshift(row[j]);
                }
                const zeros = new Array(board[i].length - mergedRow.length).fill(0);
                board[i] = mergedRow.concat([...zeros]);
        }
}

function moveRight() {
        for (let i = 0; i < board.length; i++) {
                const row = board[i].filter((e) => e !== 0);
                const mergedRow = [];
                for (let j = 0; j < row.length; j++) {
                        if (j === row.length-1) {
                                mergedRow.push(row[j]);
                                continue;
                        }
                        if (row[j] === row[j+1]) {
                                mergedRow.push(row[j]+row[j+1]);
                                score.value += row[j]+row[j+1];
                                saveHighscore();
                                j++;
                                continue;
                        }
                        mergedRow.push(row[j]);
                }
                const zeros = new Array(board[i].length - mergedRow.length).fill(0);
                board[i] = zeros.concat([...mergedRow]);
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
                for (let i = 0; i < board.length; i++) {
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
        }
}

function saveHighscore() {
        if (localStorage.getItem("highscore") >= score.value) {
                return;
        }
        localStorage.setItem("highscore", score.value);
        lifetimeHighscore.value = score.value;
}


</script>

<template>
        <main>
                <div class="flex flex-col">
                        <span>HIGHSCORE {{ score }}</span>
                        <span>LIFETIME HIGHSCORE {{ lifetimeHighscore }}</span>
                        <span>GAME OVER {{ isGameOver ? 'LOST' : 'PLAYING' }}</span>
                        <span>WINNING {{ isWon ? 'WON' : 'NOT YET' }}</span>
                        <button @click="startGame">Start</button>
                        <Board :board="board" />
                        <button @click="move('UP')" class="rounded-md bg-blue-500 p-2 w-fit">UP</button>
                        <button @click="move('DOWN')" class="rounded-md bg-blue-500 p-2 w-fit">DOWN</button>
                        <button @click="move('LEFT')" class="rounded-md bg-blue-500 p-2 w-fit">LEFT</button>
                        <button @click="move('RIGHT')" class="rounded-md bg-blue-500 p-2 w-fit">RIGHT</button>
                </div>
        </main>
</template>
