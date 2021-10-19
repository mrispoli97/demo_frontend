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
            <b-row v-if="files != null">
                <b-col style="background-color: lemonchiffon">
                    <b-card-body>
                        <b-row style="margin-bottom: 25px">
                            <b-col>
                                <b-row>
                                    <b-col>
                                        <b-card-text>
                                            <b-card-title><b>YOUR FILES</b></b-card-title>
                                        </b-card-text>
                                    </b-col>
                                </b-row>
                                <b-row v-if="files != null && 'original' in files">
                                    <b-col style="display: flex; justify-content: center">
                                        <File v-for="file in files['original']" :key="file" style="margin: 4px"
                                              :filename=file
                                              section="original"
                                              @fileSelected="fileSelectedEvent"
                                        >
                                        </File>
                                    </b-col>
                                </b-row>
                            </b-col>
                        </b-row>
                        <b-row v-for="obfuscation in ['zeros', 'random', 'junk', 'benign']" :key=obfuscation
                               style="margin-bottom: 25px">
                            <b-col v-if="obfuscation in files">
                                <b-row>
                                    <b-col>
                                        <b-card-text>
                                            <b-card-title><b>{{ obfuscation.toUpperCase() }}</b></b-card-title>
                                        </b-card-text>
                                    </b-col>
                                </b-row>
                                <b-row
                                    v-for="severity in Object.keys(files[obfuscation])" :key="severity">
                                    <b-col v-for="file in files[obfuscation][severity]" :key="file">
                                        <File
                                            :filename=file
                                            :section=obfuscation
                                            :severity=severity
                                            @fileSelected="fileSelectedEvent"
                                        >
                                        </File>
                                    </b-col>
                                </b-row>
                            </b-col>
                        </b-row>
                    </b-card-body>
                </b-col>

                <b-col v-if="fileIsSelected.status && classificationIsActive" style="background-color: #c1d3fe">
                    <Classification
                        @classifierSelected="classify"
                    >

                    </Classification>
                </b-col>

                <b-col v-if="fileIsSelected.status && obfuscationIsActive">

                </b-col>
            </b-row>
            <b-row style="margin-top: 25px; margin-bottom: 25px">
                <b-col>
                    <b-form-file
                        v-model="fileUploaded"
                        :state="Boolean(fileUploaded)"
                        placeholder="Choose a file or drop it here..."
                        drop-placeholder="Drop file here..."
                    ></b-form-file>
                    <div class="mt-3">Selected file: {{ fileUploaded ? fileUploaded.name : '' }}</div>
                </b-col>
            </b-row>
            <b-row style="margin-bottom: 25px" v-if="fileUploaded != null">
                <b-col>
                    <b-button
                        size="lg"
                        block
                        rounded
                        variant="dark"
                        style="width:50%;"
                        @click.prevent="upload"
                    >
                        <b>UPLOAD</b>
                    </b-button>
                </b-col>
            </b-row>
            <b-row style="margin-bottom: 25px" v-if="fileIsSelected.status">
                <b-col>
                    <b-button
                        size="lg"
                        block
                        rounded
                        variant="dark"
                        style="width:50%;"
                        @click.prevent="classificationIsActive=!classificationIsActive"
                    >
                        <b>SCAN</b>
                    </b-button>
                </b-col>
            </b-row>
            <b-row style="margin-bottom: 25px" v-if="fileIsSelected.status">
                <b-col>
                    <b-button
                        size="lg"
                        block
                        rounded
                        variant="dark"
                        style="width:50%;"
                        @click.prevent="obfuscationIsActive=!obfuscationIsActive"
                    >
                        <b>OBFUSCATE</b>
                    </b-button>
                </b-col>
            </b-row>
        </b-card>
    </div>
</template>

<script>

import File from './File';
import Classification from './Classification';
import Logging from "./Logging";
import axios from "axios";

export default {
    name: 'WorkSpace',
    components: {Logging, Classification, File},
    comments: {
        File,
        Classification,
        Logging,
    },
    props: {
        msg: String
    },

    data() {
        return {
            files: null,
            fileUploaded: null,
            fileIsSelected: {
                'filename': String,
                'section': String,
                'severity': null,
                'status': false,
            },
            workSpaceIsActive: true,
            classificationIsActive: false,
            obfuscationIsActive: false,

            messages: []
        }
    },
    created() {
        this.getAllFilesInTheWorkSpace();
    },
    methods: {
        getAllFilesInTheWorkSpace() {
            axios({
                method: 'get',
                url: 'get-uploaded-files',
                params: {},
            }).then(response => {
                let data = response.data;
                this.files = data;
            }).catch(error => {
                console.log("ERROR: ", error);
            });
        },
        upload() {
            let data = new FormData();
            data.append("file", this.fileUploaded);
            axios({
                method: 'post',
                url: 'upload',
                data: data,
            }).then(response => {
                this.fileUploaded = null;
                console.log("FILE UPLOADED: ", response.status);
                this.getAllFilesInTheWorkSpace();

            }).catch(error => {
                console.log("ERROR: ", error);
            });

        },
        fileSelectedEvent(event) {
            let filename = event['filename']
            let section = event['section']
            let severity = event['severity']
            if (!this.fileIsSelected.status) {
                this.fileIsSelected.filename = filename;
                this.fileIsSelected.section = section;
                this.fileIsSelected.severity = severity;
                this.fileIsSelected.status = true;
            } else {
                if (this.fileIsSelected.filename === filename && this.fileIsSelected.section === section
                    && (this.fileIsSelected.section.toString() === 'original' || this.fileIsSelected.severity === severity)) {

                    this.fileIsSelected.status = false;
                    this.fileIsSelected.filename = null;
                    this.fileIsSelected.section = null;
                    this.fileIsSelected.severity = null;

                } else {
                    this.fileIsSelected.filename = filename;
                    this.fileIsSelected.section = section;
                    this.fileIsSelected.severity = severity;
                }
            }
        },
        classify(event) {
            let data = new FormData();
            let filename = this.fileIsSelected.filename;
            let section = this.fileIsSelected.section;
            let severity = this.fileIsSelected.severity;
            let model = event['name'];

            data.append("filename", filename);
            data.append("section", section);
            data.append("severity", severity);
            data.append("model", model);

            let requestMessage = "Requested classification for ";
            requestMessage += this.get_string_message_for_file(filename, section, severity);
            requestMessage += " by the model '" + model + "'";
            this.messages.push(requestMessage);

            axios({
                method: 'post',
                url: 'classify',
                data: data,
            }).then(response => {
                this.fileUploaded = null;
                console.log("FILE CLASSIFIED: ", response);
                let responseMessage = this.get_string_message_for_file(filename, section, severity);
                responseMessage += ", has been classified as " + response.data + " by the model'" + model + "'";
                this.messages.push(responseMessage);

            }).catch(error => {
                console.log("ERROR: ", error);
            });
        },
        get_string_message_for_file(filename, section, severity) {
            let message = "File '" + filename + "', Section '" + section + "'";
            if (severity !== null) {
                message += ", Severity '" + severity + "'";
            }
            return message

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
