<template>
    <div>
        <button class="button" @click="onClick">aaaaaa</button>
    </div>
</template>

<script>
export default {
    methods: {
        async onClick() {
            try {
                const response = await this.$axios.post('http://localhost:5000/post', {
                    blinkCount: 1,
                    message: "heeeeeeeeeeee"
                })
            } catch (err) {
                console.log(err)
            }
        },
    }
}
</script>