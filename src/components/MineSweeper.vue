<template>
    <div class="body">
        <nav>
            <RouterLink to="/about">How To Play</RouterLink>
        </nav>
        <div class="topbar">
            <div class="minesCount">Mines🎀: <input type="number" v-model="minesCount" /></div>

            <div class="timer">{{ time }} seconds</div>


            <div class="grid">Grid: <input type="number" v-model="rows" /> : <input type="number" v-model="columns" /></div>
        </div>

        <select v-model="difficulty">
            <option>Easy</option>
            <option>Hard</option>
        </select>
        <button @click="startGame()" class="button" id="start">Start!</button>
        <h1>{{ result }}</h1>

        <div class="board" id="all">
            <div class="row" v-for="column in columns">
                <div :id="`${row}-${column}`" v-for="row in rows" @click="clickTile(`${row}-${column}`)">
                </div>
            </div>
        </div>

        <button class="flag" :class="{ flagdark: flagEnabled }" @click="setFlag()">🚩</button>
    </div>
</template>
<script>
import { RouterLink } from 'vue-router'

export default {
    name: 'mine-sweeper',
    props: {
    },
    data() {
        return {
            board: [],
            rows: 10,
            columns: 10,
            minesCount: 10,
            time: 0,
            minesLocation: [],
            tilesClicked: 0,
            flagEnabled: false,
            gameOver: false,
            tilecontent: '',
            result: '',
            difficulty: 'Easy',
            startGame() {
                for (let r = 1; r < this.rows; r++) {
                    let row = [];
                    for (let c = 1; c < this.columns; c++) {
                        let tileidd = r.toString() + "-" + c.toString();
                        row.push(tileidd);
                    }
                    this.board.push(row);
                }
                // console.log(this.board);
                this.setMines();
                this.startTimer();
                document.getElementById('start').classList.add('game-started')
            },
            clickTile(tileid) {
                if (this.gameOver | document.getElementById(`${tileid}`).classList.contains("tile-clicked")) {
                    return;
                }
                let tile = document.getElementById(`${tileid}`)
                if (this.flagEnabled) {

                    if (tile.innerText == "") {
                        tile.innerText = "🚩";
                    }
                    else if (tile.innerText == "🚩") {
                        tile.innerText = "";
                    }
                    return;
                }
                // console.log(tileid)

                if (this.minesLocation.includes(tileid)) {
                    alert("GAME OVER💔💔");
                    this.gameOver = true;
                    this.revealMines(tileid);
                    this.stopTimer();
                    this.result = 'you lost :(('
                    return;
                }
                if (this.difficulty == 'Hard') {
                    if (this.time >= 30) {
                        alert("you spent too much time")
                        this.gameOver = true;
                        this.revealMines(tileid);
                        this.stopTimer();
                        this.result = 'you lost :((';
                        return;
                    }
                }
                let coords = tile.id.split("-");
                let numberRow = parseInt(coords[0])
                let numberCol = parseInt(coords[1])
                this.checkifMine(numberRow, numberCol)
            },
            setFlag() {
                if (this.flagEnabled) {
                    this.flagEnabled = false
                }
                else {
                    this.flagEnabled = true
                }
            },
            setMines() {
                let minesLeft = this.minesCount;
                while (minesLeft > 0) {
                    let r = Math.floor(Math.random() * this.rows) + 1;
                    let c = Math.floor(Math.random() * this.columns) + 1;
                    let id = r.toString() + "-" + c.toString();

                    if (!this.minesLocation.includes(id)) {
                        this.minesLocation.push(id);
                        minesLeft = minesLeft - 1;
                    }
                }
                // console.log(this.minesLocation)
                return;
            },
            revealMines() {
                // console.log('rm')
                for (let row = 1; row < this.rows + 1; row++) {
                    for (let column = 1; column < this.columns + 1; column++) {
                        let tileid = `${row}-${column}`;
                        let tile = document.getElementById(`${tileid}`)
                        if (this.minesLocation.includes(tileid)) {
                            tile.innerText = "🎀";
                            tile.style.backgroundColor = "rgb(255, 129, 129)";
                        }
                    }
                }
            },
            checkifMine(r, c) {
                if (r < 1 || r > this.rows || c < 1 || c > this.columns) {
                    return;
                }

                if (document.getElementById(r + '-' + c).classList.contains('tile-clicked')) {
                    return
                }

                document.getElementById(r + '-' + c).classList.add('tile-clicked')
                this.tilesClicked += 1;

                let minesFound = 0;
                minesFound += this.checkTile(r - 1, c - 1)
                minesFound += this.checkTile(r - 1, c)
                minesFound += this.checkTile(r - 1, c + 1)

                minesFound += this.checkTile(r, c - 1)
                minesFound += this.checkTile(r, c + 1)

                minesFound += this.checkTile(r + 1, c - 1)
                minesFound += this.checkTile(r + 1, c)
                minesFound += this.checkTile(r + 1, c + 1)

                if (minesFound > 0) {
                    document.getElementById(r + '-' + c).innerText = minesFound
                    document.getElementById(r + '-' + c).classList.add('mine' + minesFound.toString())
                } else {
                    this.checkifMine(r - 1, c - 1)
                    this.checkifMine(r - 1, c)
                    this.checkifMine(r - 1, c + 1)

                    this.checkifMine(r, c - 1)
                    this.checkifMine(r, c + 1)

                    this.checkifMine(r + 1, c - 1)
                    this.checkifMine(r + 1, c)
                    this.checkifMine(r + 1, c + 1)
                }

                if (this.tilesClicked == this.rows * this.columns - this.minesCount) {
                    // this.minesCount = 'cleared!!!!!'
                    this.gameOver = true
                    document.getElementById('all').classList.add('win')
                    this.stopTimer();
                    this.result = 'you won!!'
                }

                // console.log(minesFound)
            },
            checkTile(r, c) {
                if (r < 1 || r > this.rows || c < 1 || c > this.columns) {
                    return 0;
                }
                if (this.minesLocation.includes(r + '-' + c)) {
                    return 1;
                } else {
                    return 0;
                }
            },
            startTimer() {
                this.countInterval = setInterval(() => {
                    this.time += 1;
                }, 1000);
            },
            stopTimer() {
                clearInterval(this.countInterval);
            },
        }
    },
    methods: {

    }
}
</script>

<style>
.body {
    font-weight: bold;
    text-align: center;
    color: rgb(255, 118, 141);
    /* background-color: aqua; */
}

.board {
    width: fit-content;
    /* height: 403px; */
    border: 10px solid pink;
    border-radius: 10px;
    background-color: rgb(255, 232, 235);
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
}

.board .row div {
    width: 48px;
    height: 48px;
    border: 1px solid white;
    font-size: 30px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.tile-clicked {
    background-color: pink;
}

.mine1 {
    color: red;
}

.mine2 {
    color: orange;
}

.mine3 {
    color: green;
}

.mine4 {
    color: yellow;
}

.mine5 {
    color: lightblue;
}

.mine6 {
    color: blue;
}

.mine7 {
    color: purple;
}

.mine7 {
    color: pink;
}

.flag {
    width: 100px;
    height: 50px;
    font-size: 30px;
    background-color: pink;
    border-radius: 8px;
    border: none;
    margin-top: 10px;
}

.flagdark {
    background-color: palevioletred;
}

.button {
    width: 90px;
    height: 30px;
    font-size: 20px;
    background-color: pink;
    border-radius: 8px;
    border: none;
    margin: 10px;
    color: rgb(255, 118, 141);
}

select {
    width: 90px;
    height: 30px;
    font-size: 20px;
    background-color: pink;
    border-radius: 8px;
    border: none;
    margin: 10px;
    color: rgb(255, 118, 141);
}

.win {
    background-color: lightgreen;
}

.game-started {
    display: none;
}

input {
    background-color: rgb(255, 232, 235);
    color: rgb(255, 118, 141);
    border: 1px solid rgb(255, 118, 141);
    width: 50px;
    border-radius: 8px;
}
</style>