<template>
    <div class="container">
        <div>people: {{count}}</div>
    </div>
</template>
<script>
    export default {
        name: 'chatHeader',
        props: ['socket'],
        data() {
            return {
                count: 0
            }
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