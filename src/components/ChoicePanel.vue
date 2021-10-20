<template>
    <b-row>
        <b-col>
            <b-button
                @click.prevent="filesButtonClicked"
                style="width:100%"
                squared
                variant="dark"
            >
                Files
            </b-button>
        </b-col>
        <b-col>
            <b-button
                @click.prevent="classificationButtonClicked"
                style="width:50%"
                squared
                variant="success"
            >
                Classification
            </b-button>
            <b-button
                @click.prevent="obfuscationButtonClicked"
                style="width:50%"
                squared
                variant="danger"
            >
                Obfuscation
            </b-button>
        </b-col>
    </b-row>
</template>

<script>
export default {
    name: "ChoicePanel",
    methods: {
        filesButtonClicked() {
            this.clicked("files");
        },
        classificationButtonClicked() {
            this.clicked("classification");
        },
        obfuscationButtonClicked() {
            this.clicked("obfuscation");
        },
        clicked(panel) {
            this.$emit('clickEvent', {
                'button': panel,
            });
        }
    }
}
</script>

<style scoped>

</style>