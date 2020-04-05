<template>
    <div id="register" class="animated" v-bind:class="{'shake': isError}">
        <div id="chill-gif"/>
        <input type="text" placeholder="Enter a name..." v-bind:class="{'errorInput': isError}" v-model="person.name">
        <button v-on:click="moveToHangout">Hangout</button>
    </div>
</template>
<script>
    export default {
        name: 'register',
        data() {
            return {
                isError: false,
                person: {
                    id: '',
                    name: '',
                    color: ''
                }
            }
        },
        methods: {
            toggleAnimation: function () {
                this.isError = true;
                setTimeout(() => {
                    this.isError = false;
                }, 1000);
            },
            moveToHangout: function () {
                if(!this.person.name) {
                    this.toggleAnimation();
                    return;
                }
                this.createId(10);
                this.$emit('moveToHangout', this.person)
            },
            createId: function (length) {
                let result           = '';
                let characters       = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
                let charactersLength = characters.length;
                for ( let i = 0; i < length; i++ ) {
                    result += characters.charAt(Math.floor(Math.random() * charactersLength));
                }
                this.person.id = result;
            }
        }
    }
</script>
<style scoped>
    #register{
        box-shadow: 0 10px 20px rgba(0,0,0,0.19), 0 6px 6px rgba(0,0,0,0.23);
        border-radius: 15px;
        padding: 25px;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
    #chill-gif{
        overflow: hidden;
        height: 180px;
        width: 180px;
        border-radius: 50%;
        background-image: url("../assets/chill.gif");
        background-position: center;
        background-size: cover;
        margin-bottom: 25px;
    }
    input {
        width: 100%;
        padding: 10px;
        border-radius: 10px;
        font-size: 14px;
        border: 1px solid grey;
        background-image:none;
        background-color:transparent;
        -webkit-box-shadow: none;
        -moz-box-shadow: none;
        box-shadow: none;
        outline: none;
        margin-bottom: 25px;
    }
    .errorInput{
        border-color: #C95653;
        color: #C95653;
    }
    button{
        width: 80%;
        padding: 10px;
        background-color: #E77471;
        color: white;
        outline: none;
        border: none;
        border-radius: 5px;
    }
    button:active{
        background-color: #C95653;
    }
</style>