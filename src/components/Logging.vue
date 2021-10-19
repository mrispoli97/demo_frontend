<template>
<b-row>
    <b-col>
        <b-alert
            :show="1===1"
            variant="info"
            v-for="message in messages" :key="message"
            dismissible
        >
            {{ message }}
        </b-alert>
    </b-col>
</b-row>
</template>

<script>
export default {
    name: "Logging",
    props: {
        messages: Array
    }
}
</script>

<style scoped>

</style>