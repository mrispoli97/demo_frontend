<template>
    <b-row class="Classification" style="margin-top: 25px; margin-bottom: 25px">
        <b-col style="justify-content: center">
            <b-card-title><b>MODELS</b></b-card-title>
            <b-row style="margin:20px;">
                <b-col style="display: flex; justify-content: center">
                    <ButtonCard
                        v-for="name in MLClassifiers" :key="name" style="margin: 25px;"
                        :name="name"
                        :bgColor="colors[name]"
                        :isActive="active[name]"
                        textColor="black"
                        @clicked="classifierHasBeenSelected"
                        class="align-middle"
                    >
                    </ButtonCard>
                </b-col>
            </b-row>
            <b-row style="margin:20px;">
                <b-col style="display: flex; justify-content: center">
                    <ButtonCard
                        v-for="name in DLClassifiers" :key="name" style="margin: 25px;"
                        :name="name"
                        :bgColor="colors[name]"
                        :isActive="active[name]"
                        textColor="black"
                        @clicked="classifierHasBeenSelected"
                        class="align-middle"
                    >
                    </ButtonCard>
                </b-col>
            </b-row>
        </b-col>
    </b-row>
</template>


<script>
import ButtonCard from './ButtonCard';

export default {
    name: "Classification",
    components: {
        ButtonCard,
    },

    props: {
        msg: String
    },
    created() {
        this.$emit('classifierSelected', {
            'classifier': 'LightGBM',
        });
    },
    data() {
        return {
            MLClassifiers: ['LightGBM', 'Random Forest', 'XGBoost'],
            DLClassifiers: ['MobileNet', 'ResNet', 'VGG', 'Xception'],
            colors: {
                'LightGBM': "#42a5f5",
                'Random Forest': "#c44536",
                'XGBoost': "#ff4d6d",
                'MobileNet': "#ff9500",
                'ResNet': "#55db02",
                'VGG': "#7209b7",
                'Xception': "#582f0e"
            },
            active: {
                'LightGBM': true,
                'Random Forest': false,
                'XGBoost': false,
                'MobileNet': false,
                'ResNet': false,
                'VGG': false,
                'Xception': false
            },
        }
    },
    methods: {
        classifierHasBeenSelected(event) {
            let classifierSelected = event["name"];
            this.MLClassifiers.concat(this.DLClassifiers).forEach(classifier => {
                if (classifierSelected === classifier) {
                    this.active[classifierSelected] = true;
                } else {
                    this.active[classifier] = false;
                }
            });
            this.$emit('classifierSelected', {
                'classifier': classifierSelected,
            });
        }
    }
}
</script>

<style scoped>

</style>