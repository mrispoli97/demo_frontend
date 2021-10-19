<template>
    <b-row class="Classification" style="margin-top: 30px">
        <b-col>
            <b-row style="margin-top:30px;">
                <b-col style="display: flex; justify-content: center">
                    <ClassifierCard v-for="name in MLClassifiers" :key="name" style="margin-left: 30px"
                                    :name="name"
                                    :bgColor="colors[name]"
                                    textColor="black"
                                    :emoji="emoji[name]"
                                    @classifierClicked="classifierClicked"
                    >

                    </ClassifierCard>
                </b-col>
            </b-row>
            <b-row style="margin-top:30px;">
                <b-col style="display: flex; justify-content: center">
                    <ClassifierCard v-for="name in DLClassifiers" :key="name" style="margin-left: 30px"
                                    :name="name"
                                    :bgColor="colors[name]"
                                    textColor="black"
                                    :emoji="emoji[name]"
                                    @classifierClicked="classifierClicked"
                    >

                    </ClassifierCard>
                </b-col>
            </b-row>
        </b-col>
    </b-row>
</template>


<script>
import ClassifierCard from './ClassifierCard';

export default {
    name: "Classification",
    components: {
        ClassifierCard
    },

    props: {
        msg: String
    },

    data() {
        return {
            MLClassifiers: ['LightGBM', 'Random Forest', 'XGBoost'],
            DLClassifiers: ['MobileNet', 'ResNet', 'VGG', 'Xception'],
            colors: {
                'LightGBM': "#42a5f5",
                'Random Forest': "#c44536",
                'XGBoost': "#ffddd2",
                'MobileNet': "#ff9500",
                'ResNet': "#a2e23b",
                'VGG': "#867bb0",
                'Xception': "#9c6644"
            },
            emoji: {
                'LightGBM': "&#127809;",
                'Random Forest': "&#127795;",
                'XGBoost': "&#127845;",
                'MobileNet': "&#127850;",
                'ResNet': "&#127880;",
                'VGG': "&#127917;",
                'Xception': "&#127808;"
            }
        }
    },
    methods: {
        classifierClicked(event){
            this.$emit('classifierSelected', {
                "name": event["name"],
            });
        }
    }
}
</script>

<style scoped>

</style>