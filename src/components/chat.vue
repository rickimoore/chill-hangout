<template>
  <div class="hello">
    <h1>good vibes only</h1>
    <div>
      <p v-for="(message, index) in board" v-bind:key="index">
        <template v-if="message.isIntro && message.name !== user.name">
          <span>** {{message.name}} has entered the chat **</span>
        </template>
        <template v-if="message.isOutro && message.name !== user.name">
          <span>** {{message.name}} has left the chat **</span>
        </template>
        <template v-if="!message.isIntro">
          <span>{{message.sender}}: {{message.data}}</span>
        </template>
      </p>
    </div>
    <input v-model="message" placeholder="whatsApp?..." type="text">
    <button v-on:click="submit">Submit</button>
  </div>
</template>

<script>
  import io from 'socket.io-client';
export default {
  name: 'ChatBox',
  props: {
    user: Object
  },
  data() {
    return {
      socket: {},
      board: [],
      message: ''
    }
  },
  methods: {
    submit: function () {
      this.socket.emit('message', {
        sender: this.user.name,
        data: this.message
      });
      this.message = '';
    },
    beforePageDestroyed: function () {
      this.socket.emit('onLeave', this.user);
    }
  },
  created() {
    this.socket = io(process.env.VUE_APP_SOCKET);
    console.log(process.env.VUE_APP_SOCKET);
    window.addEventListener('beforeunload', this.beforePageDestroyed)
  },
  mounted() {
    this.socket.on('introduce', data => {
      this.board.push({

        isIntro: true,
        name: data.name,
      })
    });
    this.socket.on('goodBye', data => {
      this.board.push({
        isOutro: true,
        name: data.name,
      })
    });
    this.socket.emit('onBoard', this.user);
    this.socket.on('relay', message => {
      this.board.push(message)
    });
  },

}
</script>
