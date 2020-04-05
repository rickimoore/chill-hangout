<template>
  <div id="app">
    <GameRoom v-bind:user="user" v-bind:player="player" v-if="user" />
    <Register v-on:moveToHangout="setUser" v-if="!user"/>
  </div>
</template>

<script>
import GameRoom from './components/gameroom'
import Register from './components/register'
import {Howl} from "howler";
import chill from "./assets/chill.mp3";

export default {
  name: 'App',
  data () {
    return {
      user: '',
      player: null
    }
  },
  components: {
    GameRoom, Register
  },
  methods: {
    setUser: function (data) {
      this.user = data;
    }
  },
  mounted() {
    this.player = new Howl({
      src: [chill],
      preload: true
    });
  }
}
</script>

<style>
  @import '../node_modules/animate.css/animate.min.css';
  * {
    -webkit-box-sizing: border-box; /* Safari/Chrome, other WebKit */
    -moz-box-sizing: border-box;    /* Firefox, other Gecko */
    box-sizing: border-box;         /* Opera/IE 8+ */
  }
  html, body, #app {
    height: 100%;
    width: 100%;
    margin: 0;
    padding: 0;
  }
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 25px;
}
</style>
