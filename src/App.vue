<template>
    <div class="game">
        <h1 class="title">Tic Tac Toe</h1>

        <div class="scoreboard">
            <div
                class="score"
                :class="{ active: currentPlayer === 'X' && !winner }"
            >
                <span class="player-symbol x">X</span>
                <span class="label">Player X</span>
                <span class="points">{{ scores.X }}</span>
            </div>
            <div class="score draws">
                <span class="label">Draws</span>
                <span class="points">{{ scores.draw }}</span>
            </div>
            <div
                class="score"
                :class="{ active: currentPlayer === 'O' && !winner }"
            >
                <span class="player-symbol o">O</span>
                <span class="label">Player O</span>
                <span class="points">{{ scores.O }}</span>
            </div>
        </div>

        <div class="status-bar">
            <transition name="fade" mode="out-in">
                <span v-if="winner === 'draw'" key="draw" class="status draw">
                    🤝 It's a Draw!
                </span>
                <span v-else-if="winner" key="win" class="status win">
                    🎉 Player <strong>{{ winner }}</strong> Wins!
                </span>
                <span v-else key="turn" class="status turn">
                    Player
                    <strong :class="currentPlayer.toLowerCase()">{{
                        currentPlayer
                    }}</strong
                    >'s Turn
                </span>
            </transition>
        </div>

        <div class="board">
            <button
                v-for="(cell, index) in board"
                :key="index"
                class="cell"
                :class="[
                    cell ? cell.toLowerCase() : '',
                    { winning: winningCells.includes(index) },
                    { empty: !cell && !winner },
                ]"
                :disabled="!!cell || !!winner"
                @click="makeMove(index)"
            >
                <transition name="pop">
                    <span v-if="cell" :key="cell + index">{{ cell }}</span>
                </transition>
            </button>
        </div>

        <div class="actions">
            <button class="btn reset" @click="resetBoard">New Game</button>
            <button class="btn clear" @click="clearScores">Clear Scores</button>
        </div>
    </div>

    <!-- Win Celebration Overlay -->
    <transition name="win-overlay">
        <div
            v-if="winner && winner !== 'draw'"
            class="win-overlay"
            @click="resetBoard"
        >
            <div class="confetti-container">
                <div
                    v-for="(piece, i) in CONFETTI_PIECES"
                    :key="i"
                    class="confetti-piece"
                    :style="{
                        left: piece.left + '%',
                        animationDelay: piece.delay + 's',
                        animationDuration: piece.duration + 's',
                        width: piece.width + 'px',
                        height: piece.height + 'px',
                        background: piece.color,
                        borderRadius: piece.isCircle ? '50%' : '2px',
                        transform: 'rotate(' + piece.startRotation + 'deg)',
                    }"
                ></div>
            </div>

            <div class="win-announcement">
                <span class="win-trophy">🏆</span>
                <div class="win-player" :class="winner.toLowerCase()">
                    PLAYER {{ winner }}
                </div>
                <div class="win-wins">WINS!</div>
                <div class="win-hint">click anywhere to continue</div>
            </div>
        </div>
    </transition>
</template>

<script setup>
import { ref } from "vue";

const CONFETTI_COLORS = [
    "#fc8181",
    "#63b3ed",
    "#ffd700",
    "#68d391",
    "#f6ad55",
    "#b794f4",
    "#ff69b4",
    "#00ffcc",
    "#ffffff",
];

const CONFETTI_PIECES = Array.from({ length: 80 }, (_, i) => ({
    left: Math.random() * 100,
    delay: Math.random() * 3,
    duration: 2.5 + Math.random() * 2.5,
    width: 6 + Math.floor(Math.random() * 12),
    height: 4 + Math.floor(Math.random() * 10),
    color: CONFETTI_COLORS[i % CONFETTI_COLORS.length],
    isCircle: Math.random() > 0.7,
    startRotation: Math.floor(Math.random() * 360),
}));

const board = ref(Array(9).fill(null));
const currentPlayer = ref("X");
const winner = ref(null);
const winningCells = ref([]);
const scores = ref({ X: 0, O: 0, draw: 0 });

const WINNING_COMBOS = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
];

function checkWinner(b) {
    for (const [a, c, d] of WINNING_COMBOS) {
        if (b[a] && b[a] === b[c] && b[a] === b[d]) {
            return { player: b[a], cells: [a, c, d] };
        }
    }
    if (b.every(Boolean)) return { player: "draw", cells: [] };
    return null;
}

function makeMove(index) {
    if (board.value[index] || winner.value) return;
    board.value[index] = currentPlayer.value;
    const result = checkWinner(board.value);
    if (result) {
        winner.value = result.player;
        winningCells.value = result.cells;
        scores.value[result.player]++;
    } else {
        currentPlayer.value = currentPlayer.value === "X" ? "O" : "X";
    }
}

function resetBoard() {
    board.value = Array(9).fill(null);
    winner.value = null;
    winningCells.value = [];
    currentPlayer.value = "X";
}

function clearScores() {
    scores.value = { X: 0, O: 0, draw: 0 };
    resetBoard();
}
</script>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(135deg, #1a1a2e 0%, #16213e 50%, #0f3460 100%);
    font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
    overflow-x: hidden;
}

.game {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 24px;
    padding: 32px;
    width: min(480px, 95vw);
}

.title {
    font-size: 2.4rem;
    font-weight: 800;
    color: #fff;
    letter-spacing: 2px;
    text-shadow: 0 0 20px rgba(99, 179, 237, 0.5);
}

/* Scoreboard */
.scoreboard {
    display: flex;
    gap: 12px;
    width: 100%;
    justify-content: center;
}

.score {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    padding: 14px 10px;
    background: rgba(255, 255, 255, 0.05);
    border-radius: 16px;
    border: 2px solid transparent;
    transition: all 0.3s ease;
}

.score.active {
    border-color: rgba(99, 179, 237, 0.6);
    background: rgba(99, 179, 237, 0.1);
    box-shadow: 0 0 20px rgba(99, 179, 237, 0.2);
}

.score.draws {
    max-width: 90px;
}

.player-symbol {
    font-size: 1.5rem;
    font-weight: 900;
}

.player-symbol.x {
    color: #fc8181;
}
.player-symbol.o {
    color: #63b3ed;
}

.score .label {
    font-size: 0.7rem;
    color: rgba(255, 255, 255, 0.5);
    text-transform: uppercase;
    letter-spacing: 1px;
}

.score .points {
    font-size: 1.6rem;
    font-weight: 700;
    color: #fff;
}

/* Status */
.status-bar {
    height: 36px;
    display: flex;
    align-items: center;
    justify-content: center;
}

.status {
    font-size: 1.1rem;
    color: rgba(255, 255, 255, 0.85);
}

.status strong.x {
    color: #fc8181;
}
.status strong.o {
    color: #63b3ed;
}
.status.win {
    color: #ffd700;
    font-size: 1.2rem;
}
.status.draw {
    color: #a0aec0;
    font-size: 1.2rem;
}

/* Board */
.board {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    width: 100%;
    max-width: 380px;
}

.cell {
    aspect-ratio: 1;
    background: rgba(255, 255, 255, 0.05);
    border: 2px solid rgba(255, 255, 255, 0.1);
    border-radius: 16px;
    font-size: 3rem;
    font-weight: 900;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition:
        background 0.2s,
        border-color 0.2s,
        transform 0.1s;
    color: #fff;
}

.cell.empty:hover {
    background: rgba(255, 255, 255, 0.1);
    border-color: rgba(255, 255, 255, 0.25);
    transform: scale(1.04);
}

.cell.x span {
    color: #fc8181;
}
.cell.o span {
    color: #63b3ed;
}

.cell.winning {
    background: rgba(255, 215, 0, 0.15);
    border-color: #ffd700;
    box-shadow: 0 0 20px rgba(255, 215, 0, 0.3);
}

.cell:disabled {
    cursor: default;
}

/* Buttons */
.actions {
    display: flex;
    gap: 12px;
}

.btn {
    padding: 12px 28px;
    border: none;
    border-radius: 12px;
    font-size: 0.95rem;
    font-weight: 700;
    cursor: pointer;
    letter-spacing: 0.5px;
    touch-action: manipulation;
    transition:
        transform 0.1s,
        opacity 0.2s;
}

.btn:active {
    transform: scale(0.97);
}

.btn.reset {
    background: linear-gradient(135deg, #667eea, #764ba2);
    color: #fff;
}

.btn.clear {
    background: rgba(255, 255, 255, 0.08);
    color: rgba(255, 255, 255, 0.7);
    border: 1px solid rgba(255, 255, 255, 0.15);
}

.btn:hover {
    opacity: 0.88;
}

/* Transitions */
.fade-enter-active,
.fade-leave-active {
    transition: opacity 0.2s ease;
}
.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}

.pop-enter-active {
    animation: pop 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}
@keyframes pop {
    from {
        transform: scale(0) rotate(-15deg);
        opacity: 0;
    }
    to {
        transform: scale(1) rotate(0deg);
        opacity: 1;
    }
}

/* ===================== */
/* Win Celebration       */
/* ===================== */

.win-overlay {
    position: fixed;
    inset: 0;
    z-index: 1000;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(0, 0, 0, 0.82);
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
    cursor: pointer;
    overflow: hidden;
}

.win-overlay-enter-active {
    animation: overlayFadeIn 0.4s ease both;
}
.win-overlay-leave-active {
    animation: overlayFadeOut 0.35s ease both;
}

@keyframes overlayFadeIn {
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}
@keyframes overlayFadeOut {
    from {
        opacity: 1;
    }
    to {
        opacity: 0;
    }
}

/* Announcement card */
.win-announcement {
    position: relative;
    z-index: 2;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
    text-align: center;
    animation: announcementPop 0.65s cubic-bezier(0.175, 0.885, 0.32, 1.4) 0.1s
        both;
    /* subtle glass card */
    background: rgba(255, 255, 255, 0.04);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 32px;
    padding: 48px 64px 40px;
    box-shadow: 0 30px 80px rgba(0, 0, 0, 0.5);
}

@keyframes announcementPop {
    from {
        transform: scale(0.25) rotate(-12deg);
        opacity: 0;
    }
    to {
        transform: scale(1) rotate(0deg);
        opacity: 1;
    }
}

/* Trophy */
.win-trophy {
    font-size: 5.5rem;
    line-height: 1;
    display: block;
    filter: drop-shadow(0 0 24px rgba(255, 215, 0, 0.9));
    animation: trophyRock 0.9s ease-in-out 0.7s both;
}

@keyframes trophyRock {
    0% {
        transform: rotate(-25deg) scale(0.7);
    }
    40% {
        transform: rotate(12deg) scale(1.25);
    }
    65% {
        transform: rotate(-6deg) scale(1.05);
    }
    82% {
        transform: rotate(3deg) scale(1.02);
    }
    100% {
        transform: rotate(0deg) scale(1);
    }
}

/* "PLAYER X" line */
.win-player {
    font-size: 2rem;
    font-weight: 900;
    letter-spacing: 8px;
    text-transform: uppercase;
    margin-top: 12px;
}

.win-player.x {
    color: #fc8181;
    text-shadow:
        0 0 30px #fc8181,
        0 0 60px rgba(252, 129, 129, 0.45);
}

.win-player.o {
    color: #63b3ed;
    text-shadow:
        0 0 30px #63b3ed,
        0 0 60px rgba(99, 179, 237, 0.45);
}

/* Big "WINS!" text */
.win-wins {
    font-size: clamp(4.5rem, 16vw, 7.5rem);
    font-weight: 900;
    color: #ffd700;
    letter-spacing: 10px;
    line-height: 1;
    text-shadow:
        0 0 20px #ffd700,
        0 0 60px rgba(255, 215, 0, 0.7),
        0 0 120px rgba(255, 215, 0, 0.35);
    animation: winsPulse 1.3s ease-in-out infinite alternate 0.75s;
}

@keyframes winsPulse {
    from {
        text-shadow:
            0 0 20px #ffd700,
            0 0 60px rgba(255, 215, 0, 0.7),
            0 0 120px rgba(255, 215, 0, 0.35);
        transform: scale(1);
    }
    to {
        text-shadow:
            0 0 40px #ffd700,
            0 0 100px rgba(255, 215, 0, 0.85),
            0 0 200px rgba(255, 215, 0, 0.45),
            0 0 300px rgba(255, 215, 0, 0.2);
        transform: scale(1.07);
    }
}

/* Hint text */
.win-hint {
    margin-top: 24px;
    font-size: 0.8rem;
    color: rgba(255, 255, 255, 0.35);
    letter-spacing: 3px;
    text-transform: uppercase;
    animation: hintFadeUp 0.6s ease 1.3s both;
}

@keyframes hintFadeUp {
    from {
        opacity: 0;
        transform: translateY(10px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

/* ===================== */
/* Confetti              */
/* ===================== */

.confetti-container {
    position: absolute;
    inset: 0;
    pointer-events: none;
    overflow: hidden;
    z-index: 1;
}

.confetti-piece {
    position: absolute;
    top: -18px;
    opacity: 0;
    animation: confettiFall linear infinite;
}

@keyframes confettiFall {
    0% {
        transform: translateY(0vh) translateX(0px) rotate(0deg);
        opacity: 1;
    }
    20% {
        transform: translateY(20vh) translateX(15px) rotate(120deg);
        opacity: 1;
    }
    40% {
        transform: translateY(40vh) translateX(-10px) rotate(240deg);
        opacity: 1;
    }
    60% {
        transform: translateY(60vh) translateX(20px) rotate(380deg);
        opacity: 1;
    }
    80% {
        transform: translateY(80vh) translateX(-15px) rotate(520deg);
        opacity: 0.6;
    }
    100% {
        transform: translateY(108vh) translateX(5px) rotate(720deg);
        opacity: 0;
    }
}

/* ===================== */
/* Responsive            */
/* ===================== */

/* Small phones (≤ 480px) */
@media (max-width: 480px) {
    body {
        align-items: flex-start;
        padding: 12px 0;
    }

    .game {
        gap: 16px;
        padding: 20px 16px;
        width: 100vw;
    }

    .title {
        font-size: 1.8rem;
        letter-spacing: 1px;
    }

    .scoreboard {
        gap: 8px;
    }

    .score {
        padding: 10px 6px;
        border-radius: 12px;
    }

    .score.draws {
        max-width: 72px;
    }

    .player-symbol {
        font-size: 1.2rem;
    }

    .score .label {
        font-size: 0.6rem;
    }

    .score .points {
        font-size: 1.3rem;
    }

    .status-bar {
        height: auto;
        min-height: 32px;
    }

    .status {
        font-size: 1rem;
    }

    .status.win,
    .status.draw {
        font-size: 1.05rem;
    }

    .board {
        gap: 8px;
    }

    .cell {
        font-size: 2.4rem;
        border-radius: 12px;
    }

    .actions {
        gap: 10px;
        width: 100%;
        justify-content: center;
    }

    .btn {
        padding: 12px 0;
        font-size: 0.88rem;
        flex: 1;
        max-width: 160px;
        border-radius: 10px;
    }

    /* Win overlay */
    .win-announcement {
        padding: 28px 24px 24px;
        border-radius: 24px;
        gap: 4px;
        width: 90vw;
    }

    .win-trophy {
        font-size: 3.8rem;
    }

    .win-player {
        font-size: 1.4rem;
        letter-spacing: 4px;
        margin-top: 8px;
    }

    .win-hint {
        margin-top: 16px;
        font-size: 0.72rem;
        letter-spacing: 2px;
    }
}

/* Very small phones (≤ 360px) */
@media (max-width: 360px) {
    .title {
        font-size: 1.5rem;
    }

    .cell {
        font-size: 2rem;
        border-radius: 10px;
    }

    .score .points {
        font-size: 1.1rem;
    }

    .player-symbol {
        font-size: 1rem;
    }

    .win-trophy {
        font-size: 3rem;
    }

    .win-player {
        font-size: 1.1rem;
        letter-spacing: 3px;
    }
}

/* Landscape on mobile (short screens) */
@media (max-height: 600px) and (orientation: landscape) {
    body {
        align-items: flex-start;
        padding: 8px 0;
    }

    .game {
        gap: 10px;
        padding: 12px 20px;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: center;
        width: 100vw;
        max-width: 100vw;
    }

    .title {
        width: 100%;
        text-align: center;
        font-size: 1.4rem;
        margin-bottom: -4px;
    }

    .scoreboard {
        width: 100%;
        max-width: 420px;
    }

    .status-bar {
        width: 100%;
        max-width: 420px;
        text-align: center;
        height: auto;
    }

    .board {
        max-width: 260px;
        gap: 6px;
    }

    .cell {
        font-size: 2rem;
        border-radius: 10px;
    }

    .actions {
        flex-direction: column;
        justify-content: center;
        gap: 8px;
    }

    .btn {
        padding: 10px 20px;
        font-size: 0.85rem;
    }

    .win-announcement {
        padding: 20px 32px 18px;
        gap: 2px;
    }

    .win-trophy {
        font-size: 2.8rem;
    }

    .win-player {
        font-size: 1.2rem;
        margin-top: 4px;
    }

    .win-hint {
        margin-top: 10px;
    }
}
</style>
