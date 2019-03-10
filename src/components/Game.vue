<template>
  <div class="game-component">

    <div v-if="isGameOn">
      <div class="row-container">
        <span style="font-size: 1.1rem;">Score: {{score}}</span>
      </div>
      <div class="row-container">
        <div class="board">
          <div class="board-row" v-for="(row, r) in board" :key="r">
            <div :class="getTileClass(tile.value)" v-for="(tile, t) in row" :key="t"></div>
          </div>
        </div>
      </div> 
    </div>

    <div v-if="!isGameOn">
      <div class="row-container">
        <span style="font-size: 1.6rem;">Game over!</span>
      </div>
      <div class="row-container">
        <span style="font-size: 1.1rem;">You have scored {{ score }} {{ score != 1 ? 'points' : 'point' }}</span>
      </div>
      <div class="row-container">
        <button @click="startGame()">Play again</button>
      </div>
      <div class="row-container">
        <button @click="goToMenu()">Go to menu</button>
      </div>
    </div>

  </div>
</template>

<script>
import { EventBus } from '../event-bus.js'

export default {
  name: 'Game',
  data() {
    return {
      isGameOn: false,
      isGameRunning: false,
      isGameOverVisible: false,
      score: 0,
      size: 15,
      board: [],
      snake: [],
      activeDirectionPrimary: null,
      activeDirectionSecondary: null,
      lastMoveDirection: null,
      gameSpeed: 380
    }
  },
  computed: {
    centerTile() {
      return (this.size - 1) / 2 
    },
  },
  methods: {
    startGame() {
      this.createBoard()
      this.createSnake()
      this.generateApple()
      this.isGameOn = true,
      this.score = 0,
      this.activeDirectionPrimary = null,
      this.activeDirectionSecondary = null,
      this.lastMoveDirection = null,
      this.gameSpeed = 380,
      this.$emit('game-is-on')
    },
    endGame() {
      this.isGameOn = false
      this.isGameRunning = false
      this.$emit('game-is-over')
    },
    goToMenu() {
      this.$emit('go-to-menu')
    },
    createBoard() {
      const board = []
      for (let r = 0; r < this.size; r++) {
        const row = []
        for (let c = 0; c < this.size; c++) {
          const tile = { rowIndex: r, columnIndex: c, value: null }
          row.push(tile)
        }
        board.push(row)
      }
      this.board = board
    },
    createSnake() {
      const snake = []
      const firstTile = this.board[this.centerTile][this.centerTile]
      snake.push(firstTile)
      snake[0].value = 'snake-head'
      this.snake = snake
    },
    getTileClass(value) {
      switch(value) {
        case 'snake-head':
          return 'board-tile-snake-head'
        case 'snake-body':
          return 'board-tile-snake-body'
        case 'apple':
          return 'board-tile-apple'
        default:
          return 'board-tile-empty'          
      }
    },
    handleKeyClick(direction) {
      if (this.isGameOn) {
        const validKeyValues = ['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight']
        const isKeyValid = validKeyValues.findIndex(validKey => validKey == direction)
        if (isKeyValid != -1 && this.activeDirectionPrimary != direction) {
          this.activeDirectionSecondary = this.activeDirectionPrimary
          this.activeDirectionPrimary = direction
          if (this.isGameOn && !this.isGameRunning) {
            this.isGameRunning = true
            this.runGame()
          }
        }              
      }
    },
    validateDirection(direction) {
      if (this.lastMoveDirection) {
        if (direction == 'ArrowUp' && this.lastMoveDirection == 'ArrowDown') { return false }
        if (direction == 'ArrowDown' && this.lastMoveDirection == 'ArrowUp') { return false }
        if (direction == 'ArrowLeft' && this.lastMoveDirection == 'ArrowRight') { return false }
        if (direction == 'ArrowRight' && this.lastMoveDirection == 'ArrowLeft') { return false }        
      }
      return true
    },
    moveSnake() {
      const direction = this.validateDirection(this.activeDirectionPrimary) ? this.activeDirectionPrimary : this.activeDirectionSecondary
      const nextSnakeTile = this.getNextSnakeTile(direction)
      if (nextSnakeTile) {
        if (nextSnakeTile.value == null || nextSnakeTile == this.snake[this.snake.length - 1]) {
          this.snake[this.snake.length - 1].value = null
          this.snake.pop()
          this.snake.unshift(nextSnakeTile)              
          this.snake[0].value = 'snake-head'
          if (this.snake.length > 1) {
            this.snake[1].value = 'snake-body'
          }
          this.lastMoveDirection = direction
        }
        if (nextSnakeTile.value == 'apple') {
          this.snake.unshift(nextSnakeTile)              
          this.snake[0].value = 'snake-head'
          if (this.snake.length > 1) {
            this.snake[1].value = 'snake-body'
          }
          this.lastMoveDirection = direction          
          this.generateApple()  
          this.score++
          this.updateGameSpeed()     
        }
        if (nextSnakeTile.value == 'snake-body') {
          this.endGame()
        }      
      } else {
        this.endGame()
      } 
    },
    getNextSnakeTile(direction) {
      const currentSnakeTile = this.snake[0]
      try {
        let nextSnakeTile = null
        switch (direction) {
          case 'ArrowUp':
            nextSnakeTile = this.board[currentSnakeTile.rowIndex - 1][currentSnakeTile.columnIndex]
            break
          case 'ArrowDown':
            nextSnakeTile = this.board[currentSnakeTile.rowIndex + 1][currentSnakeTile.columnIndex]
            break
          case 'ArrowLeft':
            nextSnakeTile = this.board[currentSnakeTile.rowIndex][currentSnakeTile.columnIndex - 1]
            break
          case 'ArrowRight':
            nextSnakeTile = this.board[currentSnakeTile.rowIndex][currentSnakeTile.columnIndex + 1]
            break
        }
        if (nextSnakeTile.value != 'snake-head' || nextSnakeTile.value != 'snake-body') {
          return nextSnakeTile
        } else {
          return null
        }
      } catch {
        return null
      }
    },
    generateApple() {
      console.log('generateApple')
      const generateCoordinates = () => {
        const rowIndex = Math.floor(Math.random() * this.size)
        const ColumnIndex = Math.floor(Math.random() * this.size)
        const coordinates = { rowIndex: rowIndex, columnIndex: ColumnIndex }
        console.log(coordinates)
        return coordinates
      }
      let isGenerating = true
      while (isGenerating) {
        const coordinates = generateCoordinates()
        if (this.board[coordinates.rowIndex][coordinates.columnIndex].value == null) {
          this.board[coordinates.rowIndex][coordinates.columnIndex].value = 'apple'
          isGenerating = false
        }
      }
    },
    runGame() {
      if (this.isGameOn && this.isGameRunning) {
        this.moveSnake()
        setTimeout(() => {
          this.runGame()
        }, this.gameSpeed)
      }
    },
    updateGameSpeed() {
      if (this.score % 2 == 0) {
        const speedAdjustment = Math.sqrt(this.gameSpeed)
        const updatedSpeed = this.gameSpeed - speedAdjustment 
        if (updatedSpeed > 180) {
          this.gameSpeed = updatedSpeed
        } else {
          this.gameSpeed = 180
        }
      }
    }
  },
  created() {
    EventBus.$on('key-click', (key) => {
      this.handleKeyClick(key)
    })
    window.addEventListener('keydown', ($event) => {
      const key = $event.key
      this.handleKeyClick(key)
    })
    this.startGame()
  }
}
</script>

<style scoped>
  .game {
    font-size: 1.2rem;
    background-color: var(--primary-background-color);
  }

  .board {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  .board-row {
    display: flex;
    flex-direction: row;
    justify-content: center;    
  }

  .board-row > div {
    width: 0.5rem;
    height: 0.5rem;
    margin: 0.1rem;
  }

  .board-tile-empty {
    background-color: var(--main-pixel-color);
    opacity: 0.1;
  }

  .board-tile-snake-head {
    background-color: var(--main-pixel-color);
    opacity: 1;
  }

  .board-tile-snake-body {
    background-color: var(--main-pixel-color);
    opacity: 0.7;
  }

  .board-tile-apple {
    background-color: rgb(120, 0, 0);
  }
</style>
