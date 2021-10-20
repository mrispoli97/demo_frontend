<template>
    <b-row>
        <b-col>
            <b-button
                @click.prevent="filesButtonClicked"
            >
                Files
            </b-button>
        </b-col>
        <b-col>
            <b-button
                @click.prevent="classificationButtonClicked"
            >
                Classification
            </b-button>
        </b-col>
        <b-col>
            <b-button
                @click.prevent="obfuscationButtonClicked"
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