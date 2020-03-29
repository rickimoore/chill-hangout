<template>
  <div class="window">
    <div id="content" class="chat-content">
      <div class="chat-window">
        <p class="text" v-bind:class="{'center': message.isIntro || message.isOutro}" v-for="(message, index) in board" v-bind:key="index">
          <template v-if="message.isIntro && message.name !== user.name">
            <span>** {{ message.name }} has entered the chat **</span>
          </template>
          <template v-if="message.isOutro && message.name !== user.name">
            <span>** {{ message.name }} has left the chat **</span>
          </template>
          <template v-if="!message.isIntro">
            <span>{{ message.sender }}: {{ message.data }}</span>
          </template>
        </p>
      </div>
    </div>
    <div class="input-group">
      <input
        v-model="message"
        v-on:keyup.enter="submit"
        placeholder="Say something..."
        type="text"
      />
      <button v-on:click="submit">Submit</button>
    </div>
  </div>
</template>

<script>
import io from "socket.io-client";
export default {
  name: "ChatBox",
  props: {
    user: Object
  },
  data() {
    return {
      socket: {},
      board: [],
      message: ""
    };
  },
  methods: {
    submit: function() {
      this.socket.emit("message", {
        sender: this.user.name,
        data: this.message
      });
      this.message = "";
    },
    beforePageDestroyed: function() {
      this.socket.emit("onLeave", this.user);
    },
    logMessage: function(message, type) {
      switch (type) {
        case "intro":
          this.board.push({
            isIntro: true,
            name: message.name
          });
          break;
        case "outro":
          this.board.push({
            isOutro: true,
            name: message.name
          });
          break;
        case "relay":
          this.board.push(message);
          break;
        default:
          return;
      }

      setTimeout(() => {
        const objDiv = document.getElementById("content");
        objDiv.scrollTop = objDiv.scrollHeight;
      }, 500)
    }
  },
  created() {
    this.socket = io(process.env.VUE_APP_SOCKET);
    window.addEventListener("beforeunload", this.beforePageDestroyed);
  },
  mounted() {
    this.socket.on("introduce", data => {
      this.logMessage(data, "intro");
    });
    this.socket.on("goodBye", data => {
      this.logMessage(data, "outro");
    });
    this.socket.emit("onBoard", this.user);
    this.socket.on("relay", data => {
      this.logMessage(data, "relay");
    });
  }
};
</script>

<style scoped>
.window {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.chat-content {
  width: 50%;
  display: flex;
  overflow: scroll;
  border: 1px solid #d0d0d0;
  border-radius: 15px;
  height: 400px;
}
.chat-window {
  width: 100%;
  min-height: min-content;
  padding: 10px;
}
.input-group {
  width: 50%;
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  border: 1px solid #d0d0d0;
  border-radius: 5px;
  overflow: hidden;
  margin-top: 10px;
}
.input-group input {
  width: 100%;
  border: none;
  padding: 10px;
  outline: none;
}
.input-group button {
  width: 100px;
  padding: 10px;
  background-color: #e77471;
  color: white;
  outline: none;
  border: none;
  border-radius: 5px;
}
  .text{
    margin: 5px;
    text-align: left;
  }
  .center {
    text-align: center;
  }

@media (max-width: 600px) {
  .chat-content {
    width: 80%;
  }
}
</style>
