<template>
    <b-row>
        <b-col>
            <b-card-title><b>YOUR FILES</b></b-card-title>
            <b-row style="margin: 25px">
                <b-col v-if="originalFiles.length > 0" style="display: flex; justify-content: center">
                    <File v-for="file in originalFiles" :key="file.filename" style="margin: 4px"
                          :filename=file.filename
                          :identifier="file.id"
                          :isActive="file.isActive"
                          @fileSelected="fileSelectedEvent"
                    >
                    </File>
                </b-col>
            </b-row>
            <b-row v-for="obfuscation in getObfuscationSectionsToShow()" :key=obfuscation
                   style="margin-bottom: 25px">
                <b-card-title>
                    <b>{{ obfuscation.toUpperCase() }}</b>
                </b-card-title>
                <b-col v-for="severity in getSeveritySectionsToShow(obfuscation)" :key="severity">
                    <b-card-title>{{ severity }}</b-card-title>
                    <File v-for="file in obfuscatedFiles[obfuscation][severity]" :key="file.filename"
                          style="margin: 4px"
                          :filename=file.filename
                          :identifier="file.id"
                          :isActive="file.isActive"
                          @fileSelected="fileSelectedEvent"
                    >
                    </File>
                </b-col>
            </b-row>

            <b-row style="margin: 25px;">
                <b-col>
                    <b-form-file
                        v-model="fileUploaded"
                        :state="Boolean(fileUploaded)"
                        placeholder="Choose a file or drop it here..."
                        drop-placeholder="Drop file here..."
                    ></b-form-file>
                    <div class="mt-3">Selected file: {{ fileUploaded ? fileUploaded.name : '' }}</div>
                </b-col>
                <b-col
                    style="max-width: 100px"
                >
                    <FocusButton
                        name="uploadFileButton"
                        color="#2dc653"
                        text="GO"
                        :isActive="uploadButtonIsActive"
                        @mouseIsOver="uploadButtonIsActive=true"
                        @mouseIsOut="uploadButtonIsActive=false"
                        style="margin-right: 30px"
                    >
                    </FocusButton>
                </b-col>
            </b-row>
        </b-col>
    </b-row>
</template>

<script>

import File from './File';
import axios from "axios";
import FocusButton from "@/components/FocusButton";

export default {
    name: "Files",
    components: {
        File,
        FocusButton
    },
    props: {},
    created() {
        this.getAllFilesInTheWorkSpace();
    },
    data() {
        return {
            originalFiles: [],
            obfuscatedFiles: {
                'zeros': {
                    '0.01': [],
                    '0.10': [],
                    '0.25': []
                },
                'random': {
                    '0.01': [],
                    '0.10': [],
                    '0.25': []
                },
                'junk': {
                    '0.01': [],
                    '0.10': [],
                    '0.25': []
                },
                'benign': {
                    '0.01': [],
                    '0.10': [],
                    '0.25': []
                },

            },

            fileUploaded: null,
            fileIsSelected: {
                'filename': null,
                'section': null,
                'severity': null,
                'status': false,
            },
            uploadButtonIsActive: false,
        }
    },
    methods: {
        getAllFilesInTheWorkSpace() {
            axios({
                method: 'get',
                url: 'get-uploaded-files',
                params: {},
            }).then(response => {
                let files = response.data;
                this.processFiles(files);

            }).catch(error => {
                console.log("ERROR: ", error);
            });
        },
        processFiles(files) {
            files.forEach(file => {
                let section = file['section'];
                if (section === 'original') {
                    file['isActive'] = false;
                    this.originalFiles.push(file);
                } else {
                    let severity = file['severity'];
                    file['isActive'] = false;
                    this.obfuscatedFiles[section][severity].push(file);
                }
            })

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
            let identifier = event['identifier'];

            this.originalFiles.forEach(file => {
                file.isActive = file.id === identifier;
            });

            Object.keys(this.obfuscatedFiles).forEach( obfuscation => {
                Object.keys(this.obfuscatedFiles[obfuscation]).forEach( severity => {
                    this.obfuscatedFiles[obfuscation][severity].forEach( file => {
                        file.isActive = file.id === identifier;
                    })
                })
            })

        },
        getObfuscationSectionsToShow() {
            let obfuscationsToShow = [];
            ['zeros', 'random', 'junk', 'benign'].forEach(obfuscation => {
                if (this.obfuscatedFiles[obfuscation]['0.01'].length > 0 ||
                    this.obfuscatedFiles[obfuscation]['0.10'].length > 0 ||
                    this.obfuscatedFiles[obfuscation]['0.25'].length > 0) {
                    obfuscationsToShow.push(obfuscation);
                }
            });
            return obfuscationsToShow;
        },
        getSeveritySectionsToShow(obfuscation) {
            let severityToShow = [];
            ['0.01', '0.10', '0.25'].forEach(severity => {
                if (this.obfuscatedFiles[obfuscation][severity].length > 0) {
                    severityToShow.push(severity);
                }
            });
            return severityToShow;


        }
    }
}
</script>

<style scoped>

</style>