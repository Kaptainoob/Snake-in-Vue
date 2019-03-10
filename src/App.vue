<template>
  <div id="app">
    <link href="https://fonts.googleapis.com/css?family=VT323" rel="stylesheet">
    <Phone/>
  </div>
</template>

<script>
import Phone from './components/Phone.vue'

export default {
  name: 'app',
  components: {
    Phone
  }
}
</script>

<style>
  :root {
    --main-background-color: rgb(160, 190, 0);
    --main-pixel-color: rgb(60, 60, 0);
  }

  html, body {
    margin: 0;
    padding: 1rem;
    /* background-color: rgb(164, 223, 70); */
    font-family: 'VT323', monospace;
  }

  button {
    font-family: 'VT323', monospace;
    padding: 0.2em 0.4em;
    text-align: center;
    display: inline-block;
    font-size: 1.1rem;
    border: 0.1rem solid;
    color: rgb(42, 49, 7);
    border-color: rgb(42, 49, 7);
    background-color: transparent;
  }

  button:hover:enabled {
    color: rgb(148, 206, 55);
    background-color: rgb(42, 49, 7);
    cursor: pointer;
  }

  .row-container {
    display: flex;
    flex-direction: row;
    justify-content: center;
    width: 100%;
    padding-top: 0.6rem;
    padding-bottom: 0.6rem;
  }

  .column-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    height: 100%;
  }
</style>
