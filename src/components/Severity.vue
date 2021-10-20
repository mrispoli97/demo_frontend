<template>
    <b-row>
        <b-col>
            <b-button variant="outline-success" v-bind:class="{ active: easyIsActive }" style="margin: 5px;"
                      @click.prevent="easyClicked"
            >Easy
            </b-button>
            <b-button variant="outline-warning" v-bind:class="{ active: mediumIsActive }" style="margin: 5px;"
                      @click.prevent="mediumClicked"
            >Medium
            </b-button>
            <b-button variant="outline-danger" v-bind:class="{ active: hardIsActive }" style="margin: 5px;"
                      @click.prevent="hardClicked"
            >Strong
            </b-button>
        </b-col>
    </b-row>
</template>

<script>
export default {
    name: "Severity",
    created() {
        this.$emit('severitySelected', {
            'severity': "0.01",
        });
    },
    data() {
        return {
            easyIsActive: true,
            mediumIsActive: false,
            hardIsActive: false,
        }
    },
    methods: {
        easyClicked() {
            this.easyIsActive = true;
            this.mediumIsActive = false;
            this.hardIsActive = false;
            this.emitEventClicked("0.01");
        },
        mediumClicked() {
            this.easyIsActive = false;
            this.mediumIsActive = true;
            this.hardIsActive = false;
            this.emitEventClicked("0.10");
        },
        hardClicked() {
            this.easyIsActive = false;
            this.mediumIsActive = false;
            this.hardIsActive = true;
            this.emitEventClicked("0.25");
        },
        emitEventClicked(severity) {
            this.$emit('severitySelected', {
                'severity': severity,
            });
        }
    }

}
</script>

<style scoped>

</style>