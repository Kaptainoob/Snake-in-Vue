<template>
    <div class="phone-component">
        <div class="phone-container">
            <div class="phone-antenna"></div>
            <div class="phone-body">
                <div class="phone-display">
                    <div>
                        <div>
                            <Menu v-if="activeComponent == 'menu'"
                                v-on:start-game="handleStartGame"
                            />
                            <Game v-if="activeComponent == 'game'"
                                v-on:game-is-on="handleGameIsOn"
                                v-on:game-is-over="handleGameIsOver"
                                v-on:go-to-menu="handleGoToMenu"
                            />
                        </div>
                    </div>
                </div>
                <div class="phone-keyboard">
                    <div class="keyboard-buttons-container" v-if="activeComponent == 'game' && isKeyboardVisible">
                        <div class="keyboard-button"
                            style="grid-row: 1; grid-column: 2;"
                            @click="handleKeyClick('ArrowUp')"
                        >Up</div>
                        <div class="keyboard-button"
                            style="grid-row: 3; grid-column: 2;"
                            @click="handleKeyClick('ArrowDown')"
                        >Down</div>
                        <div class="keyboard-button"
                            style="grid-row: 2; grid-column: 1;"
                            @click="handleKeyClick('ArrowLeft')"
                        >Left</div>
                        <div class="keyboard-button"
                            style="grid-row: 2; grid-column: 3;"
                            @click="handleKeyClick('ArrowRight')"
                        >Right</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import { EventBus } from '../event-bus.js'
import Game from './Game.vue'
import Menu from './Menu.vue'

export default {
    name: 'Phone',    
    components: { Game, Menu },
    data() {
        return {
            activeComponent: 'menu',
            isKeyboardVisible: false
        }
    },
    methods: {
        handleStartGame() {
            this.activeComponent = 'game'
        },
        handleGoToMenu() {
            this.activeComponent = 'menu'
        },
        handleGameIsOn() {
            this.isKeyboardVisible = true
        },
        handleGameIsOver() {
            this.isKeyboardVisible = false
        },        
        handleKeyClick(key) {
            EventBus.$emit('key-click', key)
        }
    }
}
</script>

<style>
:root {
    --phone-color: rgb(0, 58, 112);
    --display-color: rgb(162, 190, 0);
}

.phone-component {    
    display: flex;
    flex-direction: row;
    justify-content: center;
    padding: 1rem;
}

.phone-container {
    display: flex;
    flex-direction: column;
    width: max-content;
    padding: 1rem;
}

.phone-antenna {
    height: 3rem;
    width: 1.6rem;
    position: relative;
    left: 2rem;
    background-color: var(--phone-color);
    border-top-left-radius: 0.8rem;
    border-top-right-radius: 0.8rem;
}

.phone-body {   
    display: flex;
    flex-direction: column;
    width: max-content;
    padding: 2rem 1rem 1rem 1rem;
    background-color: var(--phone-color);
    border-radius: 2rem;   
}

.phone-display {    
    height: 16rem;
    width: 12rem;
    padding: 0.6rem;
    background-color: var(--display-color);
    border-radius: 0.4rem;
}

.phone-display > div {
    display: flex;
    flex-direction: row;
    justify-content: center;
    width: 100%;
    height: 100%;
}

.phone-display > div > div {
    display: flex;
    flex-direction: column;
    justify-content: center;
    height: 100%;  
}

.phone-keyboard {
    background-color: var(--phone-color);
    height: 10rem;
    padding: 1rem 0.4rem 0.2rem 0.4rem;
}

.keyboard-buttons-container {
    display: grid;
    grid-template-rows: 1fr 1fr 1fr;
    grid-template-columns: 1fr 1fr 1fr;
    height: 100%;
    width: 100%;
}

.keyboard-button {
    display: flex;
    flex-direction: column;
    justify-content: center;
    height: 100%;
    width: 100%;
    border-radius: 50%;
    background-color: lightgray;
    color: black;
    text-align: center;
    cursor: pointer;
}
</style>