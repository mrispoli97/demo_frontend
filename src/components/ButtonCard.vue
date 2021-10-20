<template>
    <b-row>
        <b-col>
            <b-row>
                <b-col>
                    <FocusButton
                        :name="name"
                        :color="bgColor"
                        :text-color="textColor"
                        :isActive="isActive"
                        @clicked="clickEvent"
                    >
                    </FocusButton>
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
import FocusButton from "@/components/FocusButton";

export default {
    name: "ButtonCard",
    components: {
        FocusButton
    },

    props: {
        name: String,
        bgColor: String,
        textColor: String,
        isActive: Boolean,

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
            this.$emit('clicked', {
                'name': this.name,
            });
        },
    }
}
</script>

<style scoped>

</style>