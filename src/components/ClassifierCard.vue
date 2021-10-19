<template>
    <b-row>
        <b-col>
            <b-row>
                <b-col>
                    <b-button
                        pill
                        size="lg"
                        class="btn-circle"
                        :style="propStyles"
                        @click.prevent="clickEvent"
                    >
                    </b-button>
                    <p v-html="emoji" class="emoji" style="margin-top: -45px;"
                        @click.prevent="clickEvent"
                    ></p>
                </b-col>
            </b-row>
            <b-row style="margin-top: 40px">
                <b-col>
                    <b>{{ name }}</b>
                </b-col>
            </b-row>
        </b-col>
    </b-row>
</template>

<script>
export default {
    name: "ClassifierCard",
    components: {},

    props: {
        name: String,
        bgColor: String,
        textColor: String,
        emoji: String

    },
    created() {
    },
    computed: {
        propStyles() {
            return {
                "background-color": this.bgColor,
                "color": this.textColor,
                "border-color": this.bgColor,
            };
        }
    },
    data() {
        return {}
    },
    methods: {
        clickEvent() {
            this.$emit('classifierClicked', {
                'name': this.name,
            });
        }
    }
}
</script>

<style scoped>

.emoji:hover {
    cursor: pointer;
}

.btn-circle {
    width: 70px;
    height: 70px;
    padding: 7px 10px;
    border-radius: 25px;
}
</style>