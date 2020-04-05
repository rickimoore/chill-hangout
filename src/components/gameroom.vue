<template>
    <div class="container">
        <div class="arena">
            <TAG v-bind:socket="socket" v-bind:user="user" />
        </div>
        <ChatBox v-bind:socket="socket" v-bind:user="user" v-bind:player="howler" v-if="user" />
    </div>
</template>

<script>
    import io from "socket.io-client";
    import ChatBox from "./chat";
    import TAG from "./games/tag";

    export default {
        name: "gameroom",
        props: {
            user: Object,
            howler: Object
        },
        components: {
            ChatBox, TAG
        },
        data() {
          return {
              socket: {},
          }
        },
        created() {
            this.socket = io(process.env.VUE_APP_SOCKET);
            window.addEventListener("beforeunload", this.beforePageDestroyed);
        },
    }
</script>

<style scoped>
    .arena{
        height: 600px;
        width: 600px;
        border: 1px solid grey;
    }
</style>