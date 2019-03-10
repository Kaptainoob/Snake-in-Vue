<template>
<div class="menu-component">
    <div class="column-container">
        <div class="row-container">
            <span style="font-size: 2rem">Snake in Vue</span>
        </div>
        <div class="row-container">
            <button class="menu-button" @click="handleStartGame">Start a game</button>
        </div>
        <div class="row-container">
            <button class="menu-button" :disabled="true">Scoreboard</button>
        </div>
        <div class="row-container">
            <button class="menu-button" :disabled="true">Options</button>
        </div>
    </div>
</div>
</template>

<script>
export default {
    name: 'Menu',
    methods: {
        handleStartGame() {
            this.$emit('start-game')
        }
    }
}
</script>

<style scoped>
    .menu-component {
        width: 100%;
    }

    .menu-button {
        width: 100%;
    }

    .menu-button:disabled {
        opacity: 0.3;
    }
</style>