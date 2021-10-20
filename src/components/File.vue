<template>
    <b-btn class="File"
           :variant="getTheme()"
           size="sm"
           @click.prevent="clickEvent"
           @mouseover="mouseOver"
           @mouseout="mouseOut"
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
        identifier: String,
        filename: String,
        isActive: Boolean,
    },
    watch: {
        isActive: function (newVal) { // watch it
            if (newVal) {
                this.theme = 'light';
            } else {
                this.theme = 'dark';
            }
        }
    },
    data() {
        return {
            theme: "dark"
        }
    },

    methods: {
        clickEvent() {
            this.$emit('fileSelected', {
                'filename': this.filename,
                'identifier': this.identifier,
            });
        },
        getTheme() {
            return this.theme;
        },
        mouseOut() {
            if (!this.isActive) {
                this.theme = "dark";
            }
        },
        mouseOver() {
            this.theme = "light";
        }
    }
}
</script>

<style scoped>

</style>