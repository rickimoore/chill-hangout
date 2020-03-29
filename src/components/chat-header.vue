<template>
    <div class="container">
        <div>people: {{count}}</div>
    </div>
</template>
<script>
    import io from "socket.io-client";
    export default {
        name: 'header',
        data() {
            return {
                socket: {},
                count: 0
            }
        },
        created() {
            this.socket = io(process.env.VUE_APP_SOCKET);
        },
        mounted() {
            this.socket.on("count", data => this.tally(data));
        },
        methods: {
            tally: function (data) {
                this.count = data;
            }
        }
    }
</script>
<style scoped>
    .container{
        width: 50%;
        display: flex;
        flex-direction: row;
        align-items: flex-end;
        justify-content: flex-end;
    }
</style>