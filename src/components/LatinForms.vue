<template>
    <div class="latin-forms" id="latinApp">
        <h1>Latin Forms</h1>
        <button v-on:click="getNextWord()" v-show="!showLatin">Start</button>
        <div v-show="showLatin">
            <div class="scoreboard">
                <span>Correct: <span class="score">{{ latinFormsScore.correct }}</span></span>
                <span>Incorrect: <span class="score">{{ latinFormsScore.total - latinFormsScore.correct }}</span></span>
            </div>
            <div v-if="selectedWord != null" class="latin-form-word">{{ selectedWord.word }}</div>
            <div v-if="selectedWord != null" class="latin-form-meaning">{{ selectedWord.meaning }}</div>
            <div>
                <span class="latin-form">{{ whichForm }}</span>
                <span class="latin-form-answer"><input v-model="latinFormsAnswer" class="latin-form-input"/></span>
            </div>
            <button v-on:click="getNextWord()" class="btn-answer">Lock in Answer</button>
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
                latinFormsAnswer: '',
                latinFormsScore: {
                    total: 0,
                    correct: 0
                },
                selectedWord: null,
                randomNumber: 0,
                index: 0,
                form: 0,
                whichForm: '2nd',
                showLatin: false
            }
        },
        methods: {
            getNextWord: function () {
                if (this.showLatin) {
                    //we already have an answer! check it.
                    //increment our question count
                    this.latinFormsScore.total++;

                    switch (this.form) {
                        case 0:
                            if (this.latinFormsAnswer === this.selectedWord.second) {
                                this.latinFormsScore.correct++;
                            }
                            break;
                        case 1:
                            if (this.latinFormsAnswer === this.selectedWord.third) {
                                this.latinFormsScore.correct++;
                            }
                        case 2:
                            if (this.latinFormsAnswer === this.selectedWord.fourth) {
                                this.latinFormsScore.correct++;
                            }
                            break;
                    }

                    this.latinFormsAnswer = '';
                }

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

                                this.showLatin = true;
                            })
                            .catch(e => {
                                this.errors.push(e)
                            });
                    })
                    .catch(e => {
                        this.errors.push(e)
                    });
            }
        }
    }
</script>

<style scoped>
    .scoreboard {
        font-size: .7em;
    }

    .score {
        font-weight: bold;
        font-size: 1.1em;
        padding-right: 1em;
    }

    .latin-form {
        padding-right: .5em;
        font-size: .8em;
        font-style: italic;
    }

    .latin-form-input {
        padding: .5em .5em .5em .5em;
    }

    .latin-form-word {
        font-weight: bold;
        margin: 1em 0em 0em 0em;
    }

    .latin-form-meaning {
        font-style: italic;
        margin: 0em 0em 1em 0em;
    }

    .btn-answer {
        margin-top: 1em;
    }
</style>