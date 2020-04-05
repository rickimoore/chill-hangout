<template>
  <canvas id="paper" ref="canvas" />
</template>

<script>
import { PaperScope, Path, Tool } from "paper";
const SPEED = 10;
const BORDER_BUMPER = 10;
export default {
  name: "TAG",
  props: ['user', 'socket'],
  data() {
    return {
      paper: new PaperScope(),
      tool: new Tool(),
      group: []
    };
  },
  mounted() {
    this.paper.setup(this.$refs.canvas);

    this.joinGame();

    this.socket.on("START_GAME", lobby => {
      lobby.forEach((player) => this.createPlayer(player))
    });
    this.socket.on("MOVE_TOKEN", data => {
      this.handleBroadCastedMovement(data)
    });

    this.tool.onKeyDown = event => this.handleMovement(event, this.user);

    this.paper.view.draw();
  },
  methods: {
    joinGame: function() {
      this.socket.emit('joinGameLobby', {
        data: this.user,
        position: {
          x: Math.floor(Math.random() * (this.$refs.canvas.clientWidth - BORDER_BUMPER)) + 1,
          y: Math.floor(Math.random() * (this.$refs.canvas.clientHeight - BORDER_BUMPER)) + 1,
        }
      });
    },
    handleBroadCastedMovement: function (data) {
      if(data.user.id !== this.user.id){
        const player = this.findPlayer(data.user.id);
        console.log(data.position)
        player.token.position.y = data.position[2];
        player.token.position.x = data.position[1]
      }
    },
    handleMovement: function (event, user) {
      const player = this.findPlayer(user.id);
      const pos = player.token.position;

      if (event.key == "up" || event.key == "w") {
        if(pos.y - 1 > BORDER_BUMPER){
          pos.y -= SPEED;
        } else {
          pos.y = BORDER_BUMPER;
        }
        this.socket.emit('MOVE_USER', {user: user, position: pos});
        return false;
      }
      if (event.key == "down" || event.key == "x") {
        if(pos.y + 1 < this.$refs.canvas.clientHeight - BORDER_BUMPER){
          pos.y += SPEED;
        } else {
          pos.y = this.$refs.canvas.clientHeight - BORDER_BUMPER;
        }
        this.socket.emit('MOVE_USER', {user: user, position: pos});
        return false;
      }
      if (event.key == "left" || event.key == "a") {
        if(pos.x - 1 > BORDER_BUMPER){
          pos.x -= SPEED;
        } else {
          pos.x = BORDER_BUMPER;
        }
        this.socket.emit('MOVE_USER', {user: user, position: pos});
        return false;
      }
      if (event.key == "right" || event.key == "d") {
        if(pos.x + 1 < this.$refs.canvas.clientWidth - BORDER_BUMPER){
          pos.x += SPEED;
        } else {
          pos.x = this.$refs.canvas.clientWidth - BORDER_BUMPER;
        }
        this.socket.emit('MOVE_USER', {user: user, position: pos});
        return false;
      }
    },
    createPlayer: function (player) {
      let newPlayer = player.data;
      newPlayer.token = new Path.Rectangle({
        x: player.position.x,
        y: player.position.y,
        width: 20,
        height: 20,
        strokeColor: "black",
        strokeWidth: 2,
        fillColor: player.data.name === this.user.name ? 'red' : 'black',
        name: "peg-"
      });

      this.group.push(newPlayer);
    },
    findPlayer: function (userId) {
      return this.group.filter((player) => player.id === userId)[0];
    }
  }
};
</script>

<style scoped>
#paper {
  width: 100%;
  height: 100%;
  margin: 0 auto;
}
</style>
