<template>
    <div id="app">
        <h1 @click="getSome">test case: creating a mask for the input field</h1>
        <InputMask type="phone" v-model='text'></InputMask>
    </div>
</template>

<script>
    import InputMask from '@/components/InputMask.vue'

    export default {
        name: 'App',
        components: {
            InputMask
        },
        data() {
            return {
                text: ''
            }
        },
        methods:{
            getSome(){
                console.log(this.text)
            }
        }

    }
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        -webkit-font-smoothing: antialiased;
        -moz-osx-font-smoothing: grayscale;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }
</style>
