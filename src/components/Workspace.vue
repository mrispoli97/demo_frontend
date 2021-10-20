<template>
    <div class="WorkSpace">
        <h1>WORK SPACE</h1>
        <b-button v-if="messages.length > 1"
                  variant="outline-primary"
                  size="sm"
                  style="margin-bottom: 10px"
                  @click.prevent="messages=[]"
        >
            close all
        </b-button>
        <Logging
            :messages="messages"
        >

        </Logging>
        <b-card
            style="min-height: 300px; color: goldenrod"
            class=""
        >
            <b-row>
                <b-col v-if="filePanelIsActive" style="background-color: lemonchiffon">
                    <Files>

                    </Files>
                </b-col>

                <b-col v-if="classificationPanelIsActive" style="background-color: #f1faee">
                    <Classification
                        @classifierSelected="classifierHasBeenSelected"
                    >

                    </Classification>
                </b-col>

                <b-col v-if="obfuscationPanelIsActive">
                    <Obfuscation
                        @obfuscationSelected="obfuscationHasBeenSelected"
                        @severitySelected="severityHasBeenSelected"
                    >
                    </Obfuscation>
                </b-col>
            </b-row>

            <b-row>
                <b-col>
                    <ChoicePanel
                        @clickEvent="panelHasBeenClicked"
                    >

                    </ChoicePanel>
                </b-col>
            </b-row>
        </b-card>
    </div>
</template>

<script>


import Classification from './Classification';
import Obfuscation from "./Obfuscation";
import ChoicePanel from "@/components/ChoicePanel";
import Logging from "./Logging";
import axios from "axios";
import Files from "@/components/Files";

export default {
    name: 'WorkSpace',
    components: {Files, ChoicePanel, Obfuscation, Logging, Classification},
    props: {
        msg: String
    },

    data() {
        return {

            filePanelIsActive: true,
            classificationPanelIsActive: false,
            obfuscationPanelIsActive: false,

            messages: [],

            severity: null,
            obfuscation: null,
            classifier: null,
        }
    },
    created() {
    },
    methods: {

        classifierHasBeenSelected(event) {
            this.classifier = event['classifier'];
        },
        obfuscationHasBeenSelected(event) {
            this.obfuscation = event['obfuscation'];
        },
        severityHasBeenSelected(event) {
            this.severity = event['severity'];
        },
        classify() {
            let data = new FormData();
            let filename = this.fileIsSelected.filename;
            let section = this.fileIsSelected.section;
            let severity = this.fileIsSelected.severity;
            let model = this.classifier;

            data.append("filename", filename);
            data.append("section", section);
            data.append("severity", severity);
            data.append("model", model);

            let requestMessage = "Requested classification for ";
            requestMessage += this.get_string_message_for_file(filename, section, severity);
            requestMessage += " by the model '" + model.toUpperCase() + "'";
            this.addMessage(requestMessage);

            axios({
                method: 'post',
                url: 'classify',
                data: data,
            }).then(response => {
                this.fileUploaded = null;
                console.log("FILE CLASSIFIED: ", response);
                let responseMessage = this.get_string_message_for_file(filename, section, severity);
                responseMessage += ", has been classified as " + response.data.toString().toUpperCase() + " by the model'" + model.toUpperCase() + "'";
                this.addMessage(responseMessage);

            }).catch(error => {
                console.log("ERROR: ", error);
            });
        },
        get_string_message_for_file(filename, section, severity) {
            let message = "File '" + filename.toUpperCase() + "', Section '" + section.toUpperCase() + "'";
            if (severity !== null) {
                message += ", Severity '" + severity.toUpperCase() + "'";
            }
            return message

        },
        addMessage(message) {

            if (this.messages.length > 2) {
                this.messages = this.messages.slice(this.messages.length - 2, 3)
            }
            this.messages.push(message);

        },
        panelHasBeenClicked(event) {
            let panel = event['button'];

            if (panel === 'files') {
                this.filePanelIsActive = !this.filePanelIsActive;
            } else if (panel === 'classification') {
                this.classificationPanelIsActive = !this.classificationPanelIsActive;
            } else {
                this.obfuscationPanelIsActive = !this.obfuscationPanelIsActive;
            }
        }


    }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

h3 {
    margin: 40px 0 0;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    display: inline-block;
    margin: 0 10px;
}

a {
    color: #42b983;
}
</style>
