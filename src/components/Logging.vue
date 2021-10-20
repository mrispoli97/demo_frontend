<template>
<b-row>
    <b-col>
        <b-alert
            :show="1===1"
            :variant="message.variant"
            v-for="message in messages" :key="message.text"
            dismissible
        >
            {{ message.text }}
        </b-alert>
    </b-col>
</b-row>
</template>

<script>
export default {
    name: "Logging",
    props: {
        messages: Array,
        variant: String,
    }
}
</script>

<style scoped>

</style>