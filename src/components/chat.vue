<template>
  <div class="chat">
    <div class="screen" v-bind:class="[screen]">
      <chatHeader v-bind:socket="socket" v-bind:screen="screen" @close="closeChat" @open="openChat" />
      <template v-if="screen === 'FULLSCREEN'">
        <div id="content" class="chat-content">
          <div class="chat-window">
            <p
                    class="text"
                    v-bind:class="{ center: message.isIntro || message.isOutro }"
                    v-for="(message, index) in board"
                    v-bind:key="index"
            >
              <template v-if="message.isIntro && message.name !== user.name">
                <span>** {{ message.name }} has entered the chat **</span>
              </template>
              <template v-if="message.isOutro && message.name !== user.name">
                <span>** {{ message.name }} has left the chat **</span>
              </template>
              <template v-if="!message.isIntro">
              <span
              ><span
                      v-bind:class="{ highlight: user.name === message.sender }"
              >{{ message.sender }}</span
              >: {{ message.data }}</span
              >
              </template>
            </p>
          </div>
        </div>
        <div class="input-group animated" v-bind:class="{ shake: isError }">
          <input
                  v-model="message"
                  v-on:keyup.enter="submit"
                  placeholder="Say something..."
                  type="text"
          />
          <button v-on:click="submit">Submit</button>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
import chatHeader from "./chat-header";

const FULLSCREEN = 'FULLSCREEN';
const HIDESCREEN = 'HIDESCREEN';

export default {
  name: "ChatBox",
  components: {
    chatHeader
  },
  props: {
    user: Object,
    player: Object,
    socket: Object,
  },
  data() {
    return {
      board: [],
      message: "",
      bufferTime: null,
      isError: false,
      screen: FULLSCREEN
    };
  },
  methods: {
    toggleAnimation: function() {
      this.isError = true;
      setTimeout(() => {
        this.isError = false;
      }, 1000);
    },
    submit: function() {
      if (!this.message) {
        this.toggleAnimation();
        return;
      }
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
      }, 500);
    },
    openChat: function () {
      this.screen = FULLSCREEN;
    },
    closeChat: function () {
      this.screen = HIDESCREEN;
    }
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

    this.socket.on("bufferMusic", time => (this.bufferTime = time));

    if (this.player) {
      // this.player.play();
    }
  }
};
</script>

<style scoped>
.chat {
  position: absolute;
  right: 0;
  top: 0;
  height: 100%;
  width: 300px;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
}
.screen{
  width: 100%;
  padding: 10px;
  display: flex;
  flex-direction: column;
}
.FULLSCREEN{
  height: 100%;
}
.HIDESCREEN {
  height: 50px;
  border: 1px solid black;
}
.chat-content {
  width: 100%;
  flex: 2;
  display: flex;
  overflow: scroll;
  border: 1px solid #d0d0d0;
  border-radius: 15px;
}
.chat-window {
  width: 100%;
  min-height: min-content;
  padding: 10px;
}
.input-group {
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
.highlight {
  color: #e77471;
}
.text {
  margin: 5px;
  text-align: left;
}
.center {
  text-align: center;
}
.count_btn {
  width: 20px;
  height: 20px;
  background-color: grey;
  border-radius: 50%;
  text-align: center;
  color: white;
  outline: none;
  border: none;
  position: absolute;
  right: 5px;
  top: 0;
  font-size: 12px;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}

@media (max-width: 600px) {
  .chat-content {
    width: 80%;
  }
}
</style>
