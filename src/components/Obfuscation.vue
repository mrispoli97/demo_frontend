<template>
    <b-row style="margin-top: 25px; margin-bottom: 25px">
        <b-col>
            <b-card-title><b>OBFUSCATION</b></b-card-title>
            <b-row style="margin: 70px">
                <b-col style="display: flex; justify-content: center">
                    <ButtonCard
                        v-for="name in obfuscations" :key="name" style="margin: 15px;"
                        :name="name"
                        :bgColor="colors[name]"
                        :isActive="active[name]"
                        textColor="black"
                        @clicked="obfuscationHasBeenSelected"
                        class="align-middle"
                    >
                    </ButtonCard>
                </b-col>
            </b-row>
            <b-row style="margin-top: 45px; margin-bottom: 45px">
                <b-col>
                    <Severity
                        @severitySelected="severityHasBeenSelected"
                    >
                    </Severity>
                </b-col>
            </b-row>
        </b-col>
    </b-row>
</template>

<script>
import Severity from "./Severity";
import ButtonCard from "@/components/ButtonCard";

export default {
    name: "Obfuscation",
    components: {ButtonCard, Severity},

    props: {
        msg: String
    },
    created() {
        this.$emit('obfuscationSelected', {
            'obfuscation': 'Zeros',
        });
    },
    data() {
        return {
            obfuscations: ['Zeros', 'Random', 'Junk', 'Benign'],
            severity: String,
            colors: {
                'Zeros': "#f72585",
                'Random': "#343a40",
                'Junk': "#78290f",
                'Benign': "#208b3a",
            },
            active: {
                'Zeros': true,
                'Random': false,
                'Junk': false,
                'Benign': false,
            }
        }
    },
    methods: {
        severityHasBeenSelected(event) {
            this.severity = event['severity'];
            this.$emit('severitySelected', {
                'severity': this.severity,
            });
        },
        obfuscationHasBeenSelected(event) {
            let obfuscationSelected = event["name"];
            Object.keys(this.active).forEach(obfuscation => {
                if (obfuscationSelected === obfuscation) {
                    this.active[obfuscationSelected] = true;
                } else {
                    this.active[obfuscation] = false;
                }
            });
            this.$emit('obfuscationSelected', {
                'obfuscation': obfuscationSelected,
            });
        }
    }
}
</script>

<style scoped>

</style>