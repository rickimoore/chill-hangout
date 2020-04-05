<template>
    <div class="container" v-bind:class="{'padding' : screen === 'FULLSCREEN'}">
        <div><font-awesome-icon icon="users" />: <span class="animated" v-bind:class="{'bounce': isAnimated}">{{count}}</span></div>
        <template v-if="screen === 'FULLSCREEN'">
            <font-awesome-icon class="icon" v-on:click="closeChat" icon="times-circle" />
        </template>
        <template v-if="screen === 'HIDESCREEN'">
            <font-awesome-icon class="icon" v-on:click="openChat" icon="expand-alt" />
        </template>
    </div>
</template>
<script>

    export default {
        name: 'chatHeader',
        props: ['socket', 'screen'],
        data() {
            return {
                count: 0,
                timer: null,
                isAnimated: false
            }
        },
        mounted() {
            this.socket.on("count", data => this.tally(data));
        },
        methods: {
            jumpTally: function() {
                this.isAnimated = true;
              this.timer = setTimeout(() => this.isAnimated = false, 1000);
            },
            tally: function (data) {
                this.count = data;
                this.jumpTally();
            },
            closeChat: function () {
                this.$emit('close', true)
            },
            openChat: function () {
                this.$emit('open', true)
            }
        }
    }
</script>
<style scoped>
    .container{
        display: flex;
        flex-direction: row;
        align-items: flex-end;
        justify-content: space-between;
    }
    .padding{
        padding: 10px;
    }
    .icon {
        font-size: 20px;
    }
</style>