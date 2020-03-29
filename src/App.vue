<template>
  <div id="app">
    <ChatBox v-bind:user="user" v-if="user" />
    <Register v-on:moveToHangout="setUser" v-if="!user"/>
  </div>
</template>

<script>
import ChatBox from './components/chat'
import Register from './components/register'

export default {
  name: 'App',
  data () {
    return {
      user: ''
    }
  },
  components: {
    ChatBox, Register
  },
  methods: {
    setUser: function (data) {
      this.user = data;
    }
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
