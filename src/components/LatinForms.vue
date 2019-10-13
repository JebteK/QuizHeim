<template>
    <div class="latin-forms" id="latinApp">
        <h1>Latin Forms</h1>
        <button v-on:click="getNextWord()" v-show="!showLatin">Start</button>
        <div v-show="showLatin">
            <div>{{ selectedWord.word }}</div>
            <div>{{ selectedWord.meaning }}</div>
            <div>
                {{ whichForm }}
            </div>
            <button v-on:click="getNextWord()">Lock in Answer</button>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        name: "LatinForms",
        data: function() {
            return {
                phrases: [
                    {word: 'pono', meaning: 'to put, place', second: 'ponere', third: 'posui', fourth: 'positus'},
                    {word: 'mitto', meaning: 'to send', second: 'mittere', third: 'misi', fourth: 'missus'},
                    {word: 'claudo', meaning: 'to close', second: 'claudere', third: 'clausi', fourth: 'clausus'},
                    {word: 'gero', meaning: 'to wage, carry on', second: 'gerere', third: 'gessi', fourth: 'gestus'},
                    {word: 'cedo', meaning: 'to yield, give way', second: 'cedere', third: 'cessi', fourth: 'cessus'},
                    {word: 'premo', meaning: 'to press, push', second: 'premere', third: 'pressi', fourth: 'pressus'},
                    {word: 'scribo', meaning: 'to write', second: 'scribere', third: 'scripsi', fourth: 'scriptus'}
                ],
                selectedWord: null,
                randomNumber: 0,
                index: 0,
                form: 0,
                whichForm: '2nd',
                showLatin: false
            }
        },
        methods: {
            getSelectedWord() {
                return this.selectedWord;
            },
            getNextWord: function () {
                axios.post("https://www.rdrand.com/API/GenerateUInt")
                    .then(response => {
                        this.randomNumber = response.data;
                        this.index = response.data % this.phrases.length;
                        this.selectedWord = this.phrases[this.index];

                        axios.post("https://www.rdrand.com/API/GenerateUInt")
                            .then(response => {
                                this.form = response.data % 3;

                                switch (this.form) {
                                    case 0:
                                        this.whichForm = '2nd';
                                        break;
                                    case 1:
                                        this.whichForm = '3rd';
                                        break;
                                    case 2:
                                        this.whichForm = '4th';
                                        break;
                                }
                            })
                            .catch(e => {
                                this.errors.push(e)
                            });
                    })
                    .catch(e => {
                        this.errors.push(e)
                    });
            }
        },
        created: function () {
            this.selectedWord = this.phrases[0];
        }
    }
</script>

<style scoped>

</style>