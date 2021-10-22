<template>
    <div class="WorkSpace">
        <h1>WORK SPACE</h1>
        <b-button v-if="this.messages.length > 1"
                  variant="outline-primary"
                  size="sm"
                  style="margin-bottom: 10px"
                  @click.prevent="clearMessages"
        >
            <small>CLEAR ALL</small>
        </b-button>
        <Logging
            :messages="messages"
            variant="success"
            style="min-height: 100px"
        >
        </Logging>
        <b-card
            style="min-height: 300px; color: goldenrod"
            class=""
        >
            <b-row>
                <b-col v-if="filePanelIsActive"
                       style="display: flex;
                       justify-content: center;
                background-color: #248277">
                    <Files
                        @fileSelected="fileHasBeenSelected"
                        ref="filesComponent"
                    >
                    </Files>
                    <FocusButton v-if="Boolean(file) && Boolean(classifier) && classificationPanelIsActive"
                                 text="🛡️"
                                 color="goldenrod"
                                 :isActive="classifyButtonIsActive"
                                 @mouseIsOver="classifyButtonIsActive=false"
                                 @mouseIsOut="classifyButtonIsActive=true"
                                 @clicked="classify"
                                 style="z-index: 2;
                                 position: fixed;
                                 top: 50%;
                                 left: 50%;
                                 transform: translate(-50%,-50%);"
                    ></FocusButton>
                    <FocusButton
                        v-if="Boolean(file) &&
                        Boolean(obfuscation) &&
                        Boolean(severity) &&
                         obfuscationPanelIsActive"
                        text="👾"
                        color="goldenrod"
                        :isActive="obfuscationButtonIsActive"
                        @mouseIsOver="obfuscationButtonIsActive=false"
                        @mouseIsOut="obfuscationButtonIsActive=true"
                        @clicked="obfuscate"
                        style="z-index: 2;
                        position: fixed;
                        top: 50%;
                        left: 50%;
                        transform: translate(-50%,-50%);"
                    ></FocusButton>
                </b-col>

                <b-col v-if="classificationPanelIsActive" style="background-color: #350020">
                    <Classification
                        @classifierSelected="classifierHasBeenSelected"
                    >

                    </Classification>
                </b-col>

                <b-col v-if="obfuscationPanelIsActive" style="background-color: #22002a">
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
import FocusButton from "@/components/FocusButton";

export default {
    name: 'WorkSpace',
    components: {Files, ChoicePanel, Obfuscation, Logging, Classification, FocusButton},
    props: {
        msg: String
    },

    data() {
        return {

            filePanelIsActive: true,
            classificationPanelIsActive: false,
            obfuscationPanelIsActive: false,

            messages: [],

            severity: "",
            obfuscation: "",
            classifier: "",
            file: null,

            classifyButtonIsActive: true,
            obfuscationButtonIsActive: true,


        }
    },
    created() {
    },
    methods: {
        fileHasBeenSelected(event) {
            this.file = event['file'];
        },
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
            let filepath = this.file.filepath;
            let model = this.classifier;

            let filename = this.file.filename;
            // let section = this.file.section;
            // let severity = this.file.severity;

            let label = filename.split("-")[0].replace(" ", "");

            data.append("filepath", filepath);
            data.append("model", model);

            // let requestMessage = "Requested classification for ";
            // requestMessage += this.get_string_message_for_file(filename, section, severity);
            // requestMessage += ". Model: " + model.toUpperCase() + "";
            // this.messages = this.addMessage(this.messages, requestMessage, 'success');

            axios({
                method: 'post',
                url: 'classify',
                data: data,
            }).then(response => {
                this.fileUploaded = null;
                let prediction = response.data.toString();
                // let responseMessage = this.get_string_message_for_file(filename, section, severity);
                let responseMessage = model.toUpperCase()+" : " + prediction.toUpperCase();

                if(prediction === label){
                    responseMessage += " 👍"
                    this.messages = this.addMessage(this.messages, responseMessage, 'info');
                }else{
                    responseMessage += " 👎"
                    this.messages = this.addMessage(this.messages, responseMessage, 'danger');
                }


            }).catch(error => {
                console.log("ERROR: ", error);
            });
        },
        obfuscate() {
            let data = new FormData();
            let filepath = this.file.filepath;
            let obfuscation = this.obfuscation;
            let obf_severity = this.severity;

            console.log("OBF SEVERITY: " + obf_severity);

            let filename = this.file.filename;
            let section = this.file.section;
            let severity = this.file.severity;

            data.append("filepath", filepath);
            data.append("obfuscation", obfuscation.toLowerCase());
            data.append("severity", obf_severity);

            // let requestMessage = "Requested obfuscation for ";
            // requestMessage += this.get_string_message_for_file(filename, section, severity);
            // requestMessage += ". Technique: " + obfuscation.toUpperCase() + "";
            // this.messages = this.addMessage(this.messages, requestMessage, 'success');

            axios({
                method: 'post',
                url: 'obfuscate',
                data: data,
            }).then(() => {
                this.fileUploaded = null;
                this.$refs.filesComponent.getAllFilesInTheWorkSpace();
                let responseMessage = this.get_string_message_for_file(filename, section, severity);
                responseMessage += " obfuscated";
                this.messages = this.addMessage(this.messages, responseMessage, 'info');

            }).catch(error => {
                console.log("ERROR: ", error);
            });
        },
        get_string_message_for_file(filename, section, severity) {
            let today = new Date();
            let time = today.getHours() + ":" + today.getMinutes() + ":" + today.getSeconds();
            if(section === 'original'){
                section = 'your files';
            }
            let message = time+"   [ " + section.toUpperCase() + " ]";
            if (severity !== null) {
                message += "[ " + severity.toUpperCase() + " ]";
            }
            message += "[ " + filename.toUpperCase() + " ]"
            return message

        },
        addMessage(messages, message, variant) {

            if (messages.length > 2) {
                messages = messages.slice(messages.length - 2, messages.length);
            }
            messages.push({
                'text': message,
                'variant': variant,
            });
            return messages
        },
        clearMessages() {
            this.messages = [];
        },
        panelHasBeenClicked(event) {
            let panel = event['button'];

            if (panel === 'files') {
                this.filePanelIsActive = !this.filePanelIsActive;
            } else if (panel === 'classification') {
                this.classificationPanelIsActive = !this.classificationPanelIsActive;
                if (this.classificationPanelIsActive && this.obfuscationPanelIsActive) {
                    this.obfuscationPanelIsActive = false;
                }
            } else {
                this.obfuscationPanelIsActive = !this.obfuscationPanelIsActive;
                if (this.classificationPanelIsActive && this.obfuscationPanelIsActive) {
                    this.classificationPanelIsActive = false;
                }
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
