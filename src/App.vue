<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
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
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
