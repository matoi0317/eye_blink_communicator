<template>
    <div>
        <button class="button" @click="a">Button1</button>
        <button class="button" @click="b">Button2</button>
        <button class="button" @click="c">Button3</button>
    </div>
</template>

<script>
export default{
    methods:{
        async a(){
            const response = await this.$axios.post('http://localhost:5000/post', {
                a: 1
            })
            console.log(response)
        },
        async b(){
            const response = await this.$axios.post('http://localhost:5000/post', {
                a: 2
            })
        },
        async c(){
            const response = await this.$axios.post('http://localhost:5000/post', {
                a: 3
            })
        }
    }
}
</script>
