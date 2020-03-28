<template>
    <div>
        <p>sign in form</p>
        <input type="text" v-model="person.name">
        <button v-on:click="moveToHangout">Hangout</button>
    </div>
</template>
<script>
    export default {
        name: 'register',
        data() {
            return {
                person: {
                    name: '',
                    color: ''
                }
            }
        },
        methods: {
            moveToHangout: function () {
                this.$emit('moveToHangout', this.person)
            }
        }
    }
</script>