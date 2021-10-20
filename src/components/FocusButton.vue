<template>
    <div>
        <b-button
            pill
            class="btn-circle"
            :style="propStyles"
            :active="isActive"
            @click="clickEvent"
            @mouseover="mouseIsOver"
            @mouseout="mouseOut"
        >
            <span v-html="text"></span>
        </b-button>

    </div>


</template>

<script>
export default {
    name: "FocusButton",
    components: {},

    props: {
        name: String,
        color: String,
        textColor: String,
        isActive: Boolean,
        text: {
            required: false,
            default: "",
        }
    },
    created() {
    },
    computed: {
        propStyles() {
            return {
                "background-color": this.getBackgroundColor(),
                "color": this.textColor,
                "border-color": this.getBorderColor(),
                "border-width": "2px"
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
        getBackgroundColor() {
            if (!this.isActive) {
                return this.color;
            } else {
                return "transparent"
            }
        },
        getBorderColor() {
            if (this.isActive) {
                return this.color;
            } else {
                return "transparent"
            }
        },
        mouseIsOver(event) {
            this.$emit('mouseIsOver', {
                'event': event,
                'name': this.name,
            });
        },
        mouseOut(event) {
            this.$emit('mouseIsOut', {
                'event': event,
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