<template>
    <b-btn class="File"
           variant="dark"
           size="sm"
           @click.prevent="clickEvent"
    >
        <b-row>
            <b-col>
                <p>&#128196;</p>
            </b-col>
        </b-row>
        <b-row>
            <b-col>
                <p><b>{{ filename }}</b></p>
            </b-col>
        </b-row>
    </b-btn>
</template>

<script>
export default {
    name: "File",
    props: {
        filename: String,
        section: String,
        severity: {
            type: String,
            default: null,
        }
    },
    data() {
        return {
            imageURL: "https://d2gg9evh47fn9z.cloudfront.net/800px_COLOURBOX9748777.jpg",
        }
    },
    methods: {
        clickEvent(){
            this.$emit('fileSelected', {
                'filename': this.filename,
                'section': this.section,
                'severity': this.severity
            });
        }
    }
}
</script>

<style scoped>

</style>